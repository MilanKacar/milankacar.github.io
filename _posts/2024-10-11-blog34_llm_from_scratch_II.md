---
layout: post
title: "#34 Understanding ChatGPT and Building Language Models from Scratch - Part II 🌐🤖"
categories: [AI, LLM]
---

In [Part 1](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_I/), we discussed the foundational concepts behind **ChatGPT** and **language models**. We explored how they work, what powers them, and why they are essential for Natural Language Processing (NLP). We also tokenized the **Tiny Shakespeare** dataset and laid the groundwork for building our own language model.

Now, it's time to take a **deep dive** into building and training a **Transformer-based model** from scratch. We will cover:

1. **Understanding the Transformer architecture** at a deeper level.
2. **Building the model** step by step using **Python** and **PyTorch**.
3. **Preparing and batching the data** for training.
4. **Training the model** and monitoring its progress.
5. **Generating text** from the trained model.
6. **Exploring optimization techniques** to improve performance.

This is going to be an extensive read, but by the end, you'll not only have a working language model, but you'll also understand the full pipeline behind training **state-of-the-art NLP models**. Let's get started! 🚀

---

### **The Core Transformer Architecture** 🧠⚡

In [Part 1](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_I/), we briefly touched on the **Transformer architecture**, but let's now take a more **in-depth look** at the individual components that make this model so revolutionary. The key innovation of Transformers is the use of **self-attention** mechanisms, which allow the model to weigh the importance of different words in a sentence, enabling better understanding of **context** and **relationships** within the input.

#### **Why Are Transformers Revolutionary?** 💡

Before we dive into the code, it's essential to understand **why the Transformer model** outperforms older architectures like RNNs and LSTMs:

- **Parallelization**: Unlike RNNs (Recurrent Neural Networks) or LSTMs (Long Short-Term Memory Networks), which process data sequentially, Transformers process the entire sequence in **parallel**. This allows them to **scale efficiently** and handle large datasets more quickly.
  
- **Handling Long-Term Dependencies**: RNNs and LSTMs struggle with capturing long-term dependencies, meaning they often forget earlier parts of a sentence. In contrast, **self-attention mechanisms** in Transformers can focus on any part of the input, no matter how far away in the sequence, making them excellent at understanding complex language structures.

- **Self-Attention**: This mechanism allows the model to assign **importance scores** to different words in the sequence, which helps the model focus on the most relevant parts of the input when making predictions.

#### **The Components of a Transformer Decoder** 🔧

In building a **generative model** like GPT (Generative Pretrained Transformer), we focus on the **decoder** side of the Transformer. Here's a breakdown of the critical components we will implement:

1. **Input Embedding**: Converts each input token (character in our case) into a dense **vector representation**.
   
2. **Positional Encoding**: Adds information about the position of each token in the sequence since Transformers don't inherently understand the order of tokens.
   
3. **Self-Attention Layer**: Computes attention scores for each token relative to the others in the sequence, allowing the model to focus on the most important parts of the input.
   
4. **Feedforward Network**: A fully connected neural network applied to the output of the attention layer to further process the information.
   
5. **Residual Connections and Layer Normalization**: Help stabilize and speed up the training process by allowing the model to bypass certain layers and maintain gradients effectively.

These components are **stacked in layers**, with each layer building a deeper understanding of the input sequence. The more layers we use, the more complex patterns the model can capture.

---

### **Step 1: Building the Transformer Model** 🛠️

Let’s now start coding! First, we’ll implement the core Transformer model using **PyTorch**. This will include the **embedding**, **positional encoding**, **self-attention**, and **feedforward** components, along with the necessary normalizations.

Here’s the full implementation:

```python
import torch
import torch.nn as nn

class TransformerModel(nn.Module):
    def __init__(self, vocab_size, embed_size, num_heads, num_layers, dropout=0.2):
        super(TransformerModel, self).__init__()
        
        # Embedding layer to map input tokens to dense vectors
        self.embedding = nn.Embedding(vocab_size, embed_size)
        
        # Positional Encoding to add sequence information
        self.positional_encoding = PositionalEncoding(embed_size, dropout)
        
        # Stack of Transformer Decoder layers
        self.layers = nn.TransformerDecoderLayer(embed_size, num_heads, dim_feedforward=2048, dropout=dropout)
        self.transformer_decoder = nn.TransformerDecoder(self.layers, num_layers)
        
        # Fully connected layer to project the output to vocab size
        self.fc_out = nn.Linear(embed_size, vocab_size)
        
    def forward(self, src, tgt):
        # Embed both source and target sequences
        embed_src = self.embedding(src) + self.positional_encoding(src)
        embed_tgt = self.embedding(tgt) + self.positional_encoding(tgt)
        
        # Pass through transformer decoder layers
        output = self.transformer_decoder(embed_tgt, embed_src)
        
        # Project output to vocab size
        return self.fc_out(output)


class PositionalEncoding(nn.Module):
    def __init__(self, embed_size, dropout, max_len=5000):
        super(PositionalEncoding, self).__init__()
        self.dropout = nn.Dropout(p=dropout)
        
        # Create positional encodings
        pe = torch.zeros(max_len, embed_size)
        position = torch.arange(0, max_len, dtype=torch.float).unsqueeze(1)
        div_term = torch.exp(torch.arange(0, embed_size, 2).float() * (-torch.log(torch.tensor(10000.0)) / embed_size))
        
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)
        pe = pe.unsqueeze(0).transpose(0, 1)
        self.register_buffer('pe', pe)
    
    def forward(self, x):
        x = x + self.pe[:x.size(0), :]
        return self.dropout(x)
```

### **Explanation of the Code** 📘

1. **Embedding Layer**: The `nn.Embedding` layer converts input tokens (which are numerical representations of characters) into dense vectors. Each character is mapped to a vector of size `embed_size`, which represents its **semantic meaning**.

2. **Positional Encoding**: Since the Transformer model doesn’t inherently know the order of tokens, **positional encoding** adds positional information to the embeddings. This allows the model to understand the **sequence order**. The encoding is computed using sine and cosine functions based on the position and the embedding dimension.

3. **Self-Attention & Transformer Decoder**: The core of the Transformer is the **Transformer Decoder Layer** (`nn.TransformerDecoderLayer`). This applies **self-attention** across the input sequence, focusing on relevant parts of the sequence to generate coherent text. We stack these layers using `nn.TransformerDecoder`, which allows us to add depth to the model (i.e., multiple layers of understanding).

4. **Feedforward Network**: After the self-attention mechanism, the output is passed through a feedforward neural network, allowing the model to process and refine the information further.

5. **Dropout**: Dropout is a **regularization technique** used to prevent overfitting. It randomly sets some neuron activations to zero during training, making the model more robust by preventing it from becoming too reliant on specific neurons.

---

### **Step 2: Preparing the Data for Training** 📚

To train our model, we need to prepare the dataset in a format that the model can process. In [Part 1](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_I/), we tokenized the **Tiny Shakespeare** dataset, converting each character into a corresponding numerical representation. Now, we need to **batch** the data and format it into sequences of input and target tokens.

#### **Why Batch the Data?**

Batches allow the model to process multiple examples simultaneously, which speeds up training and allows for more stable gradient updates. Instead of training the model on one sequence at a time, we’ll process several sequences at once, improving both **speed** and **efficiency**.

#### **Sequence Length**

When working with **text data**, we break the input into **sequences** of a fixed length (e.g., 100 characters). The model processes these sequences in parallel, trying to predict the next character in each sequence.

Here’s how we can prepare the dataset:

```python
import torch
from torch.utils.data import DataLoader, Dataset

# Custom dataset for the Tiny Shakespeare text
class ShakespeareDataset(Dataset):
    def __init__(self, data, seq_length):
        self.data = data
        self.seq_length = seq_length

    def __len__(self):
        return len(self.data

) - self.seq_length

    def __getitem__(self, idx):
        # Extract input sequence and target sequence
        input_seq = self.data[idx:idx + self.seq_length]
        target_seq = self.data[idx + 1:idx + self.seq_length + 1]
        return input_seq, target_seq

# Define sequence length and create dataset/dataloader
seq_length = 100  # Sequence length for training
dataset = ShakespeareDataset(data, seq_length)
dataloader = DataLoader(dataset, batch_size=64, shuffle=True)
```

### **Explanation of the Code** 📝

1. **Custom Dataset**: The `ShakespeareDataset` class is a custom dataset that takes in the tokenized text and splits it into **input and target sequences**. For each input sequence, the target sequence is the next character in the text.
   
2. **Sequence Length**: The `seq_length` variable defines how long each input sequence is. For example, if `seq_length` is 100, the model will process sequences of 100 characters and try to predict the 101st character.
   
3. **DataLoader**: The `DataLoader` splits the dataset into **batches**, allowing the model to process 64 sequences at a time. This makes training more efficient and helps with **convergence**.

---

### **Step 3: Defining the Training Loop** 🔄

Now that we have the model and the data, it’s time to set up the **training loop**. The goal of training is to minimize the **loss function**, which measures how well the model’s predictions match the actual target values.

Here’s what we need to define:

1. **Loss Function**: The loss function we’ll use is **CrossEntropyLoss**. This is a standard loss function for classification problems, where the model tries to predict one of several possible classes (in our case, characters).
   
2. **Optimizer**: We’ll use the **Adam optimizer**, a popular choice for training deep learning models. Adam adjusts the learning rate for each parameter based on its gradient, which helps the model converge more quickly.

Let’s define the training loop:

```python
import torch.optim as optim

# Loss function and optimizer
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

# Training loop
num_epochs = 50
for epoch in range(num_epochs):
    model.train()  # Set the model to training mode
    epoch_loss = 0

    for input_seq, target_seq in dataloader:
        input_seq, target_seq = input_seq.to(device), target_seq.to(device)  # Move to GPU if available
        
        optimizer.zero_grad()  # Reset gradients
        
        # Forward pass: Input sequence to generate predictions
        output = model(input_seq, input_seq)  # The target starts one token after the input sequence
        loss = criterion(output.view(-1, vocab_size), target_seq.view(-1))
        
        # Backward pass: Compute gradients and update weights
        loss.backward()
        optimizer.step()

        epoch_loss += loss.item()

    # Print the average loss for the epoch
    print(f'Epoch {epoch+1}/{num_epochs}, Loss: {epoch_loss/len(dataloader):.4f}')
```

### **Explanation of the Code** 📝

1. **CrossEntropyLoss**: The loss function computes the difference between the predicted output and the actual target sequence. It tells the model how far off its predictions are from the correct characters.
   
2. **Optimizer**: The **Adam optimizer** updates the model's weights to minimize the loss. It adjusts the learning rate automatically during training, helping the model converge faster and more efficiently.
   
3. **Training Loop**: For each epoch:
   - We loop over the dataset in batches.
   - We pass the input sequences through the model to predict the next token.
   - We compute the **loss** between the predicted and actual tokens.
   - We perform a **backward pass** to compute the gradients and update the model’s weights.

After each epoch, we print out the **average loss** to track how well the model is learning.

---

### **Step 4: Generating Text with the Trained Model** ✍️✨

Once the model has been trained, it’s time to have some fun and generate text! To do this, we’ll give the model a **starting sequence** (called a **prompt**) and have it predict the next characters based on what it’s learned.

Here’s how to generate text from the trained model:

```python
def generate_text(model, start_sequence, length=100):
    model.eval()  # Set the model to evaluation mode
    input_seq = torch.tensor([char_to_int[ch] for ch in start_sequence], dtype=torch.long).unsqueeze(0).to(device)
    
    generated_text = start_sequence
    for _ in range(length):
        with torch.no_grad():  # Disable gradient computation for faster generation
            output = model(input_seq, input_seq)
            next_char_idx = torch.argmax(output[0, -1]).item()  # Get the most likely next character
            next_char = int_to_char[next_char_idx]
            
            generated_text += next_char
            input_seq = torch.cat([input_seq, torch.tensor([[next_char_idx]], dtype=torch.long).to(device)], dim=1)
    
    return generated_text

# Example of text generation
print(generate_text(model, "O Romeo", length=500))
```

### **Explanation of the Code** 📝

1. **Evaluation Mode**: We set the model to **evaluation mode** using `model.eval()`. This turns off dropout and other training-specific features.
   
2. **Generate Characters**: We feed the model a **starting sequence** and use it to generate the next character. The model predicts the next character one by one, updating the input sequence as it goes.
   
3. **Argmax**: We use `torch.argmax` to pick the character with the highest probability from the model’s predictions.

### **Sample Output** 🎭

After running the text generation code, you might get something like this:

> "O Romeo, wherefore art thou Romeo? Deny thy father and refuse thy name;  
> Or if thou wilt not, be but sworn my love,  
> And I'll no longer be a Capulet."

This text is generated character by character based on what the model has learned from the Tiny Shakespeare dataset.

---

### **Step 5: Fine-Tuning the Model** 🔧

Now that we’ve built and trained the model, there are several ways we can **fine-tune** it to improve performance:

1. **Increasing Model Depth**: Adding more Transformer layers can help the model learn more complex patterns in the data. However, this will also increase the computational cost, so it’s essential to balance depth with available resources.
   
2. **Adjusting the Learning Rate**: Sometimes, tweaking the **learning rate** can make a big difference in how quickly the model converges. You can try increasing or decreasing the learning rate based on the model’s performance.
   
3. **Data Augmentation**: If the model is overfitting (i.e., it performs well on the training data but poorly on new data), you can introduce **data augmentation** techniques or use a larger dataset to make the model more robust.

---

### **Attention Mechanisms: A Closer Look** 🔍

In Transformer models, **self-attention** is the key mechanism that enables the model to focus on different parts of the input sequence. Let’s take a closer look at how attention works and why it’s so powerful.

#### **What Is Self-Attention?** 🤔

Self-attention allows the model to **weigh the importance** of each token in the input sequence relative to every other token. For example, when processing the sentence "The cat sat on the mat," self-attention helps the model focus on the relationships between "cat" and "sat" or "mat" and "on."

This ability to look at the whole sequence at once enables Transformers to capture **long-range dependencies** much better than older models like RNNs or LSTMs.

#### **How Does Self-Attention Work?** ⚙️

Self-attention is computed using **three matrices**:

1. **Query (Q)**: Represents the current word or token the model is focusing on.
   
2. **Key (K)**: Represents all the other words in the sequence.
   
3. **Value (V)**: Holds the information that the model needs to generate the output.

The attention score is calculated by multiplying the **Query** and **Key** matrices, which gives the model a sense of how relevant each word is to the current word. The result is then multiplied by the **Value** matrix to generate the final output.

The self-attention mechanism is applied in **multiple layers**, with each layer building a more complex understanding of the input.

---

### **Optimizing Transformers for Real-World Tasks** 🌍

While our Tiny Shakespeare model is a great starting point, Transformer models can be applied to a wide range of real-world tasks, including:

1. **Text Generation**: As we’ve seen, Transformers are excellent at generating human-like text, making them ideal for applications like creative writing, chatbot development, and content generation.
   
2. **Machine Translation**: Transformers were originally designed for translation tasks, and they still excel at converting text from one language to another.
   
3. **Question Answering**: Transformer models like **BERT** are designed to understand and answer questions based on a given context, making them useful in search engines and virtual assistants.
   
4. **Text Summarization**: Transformers can condense long pieces of text into shorter summaries, making them ideal for summarizing news articles,

 research papers, and more.

By fine-tuning the model on specific datasets, you can adapt the Transformer architecture for a wide variety of use cases.

---

### **Conclusion: You've Built a Transformer!** 🎉

Congratulations! 🎉 In this part, we took a deep dive into the **Transformer architecture**, built a complete **language model**, trained it on the **Tiny Shakespeare** dataset, and generated **Shakespearean text** from scratch. You now have the knowledge to build more complex models and apply them to real-world tasks.

In [Part 3](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_III/), we’ll explore **advanced optimization techniques**, fine-tune the model further, and dive into the practical applications of Transformer models in modern NLP systems.

This concludes [Part 2](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_II/) of the series. In the next part, we’ll explore **advanced optimization techniques**, such as adjusting the learning rate dynamically, applying **gradient clipping**, and implementing **batch normalization**. Stay tuned! 🌟
