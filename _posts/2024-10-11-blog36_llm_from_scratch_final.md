---
layout: post
title: "#36 MyGPT Building and Deploying a Large Language Model (LLM) with Python 🛠️💻"
categories: [AI, LLM]
---

Now that we've gone through the theory and some optimizations in the previous parts of this blog series, it’s time to put everything together and build a complete **Large Language Model (LLM)** from scratch. If you haven't already, I encourage you to check out the earlier posts in this series to understand the foundational concepts and techniques:

- [**Part 1: Introduction to Transformers and LLMs**](https://milankacar.github.io/ai/llm/blog33_llm_from_scratch_I/): This post introduces the basics of Transformers, language models, and how they form the backbone of modern NLP tasks.
  
- [**Part 2: Building and Training a Transformer Model**](https://milankacar.github.io/ai/llm/blog34_llm_from_scratch_II/): Here, we covered how to set up a Transformer model, train it using PyTorch, and understand the training pipeline.
  
- [**Part 3: Optimizations and Real-World Applications**](https://milankacar.github.io/ai/llm/blog35_llm_from_scratch_III/): In this part, we explored advanced optimization techniques, regularization, and how to apply Transformer models to real-world tasks.

In this final part, we’ll focus primarily on **code**—applying everything we’ve covered so far in a hands-on, practical implementation. You’ll be able to build a working **LLM**, train it, generate text, and fine-tune it based on the principles discussed earlier. Let’s dive right into it!
We’ll build, train, save, and use our LLM, ensuring it's fully operational. The model will be based on a **Transformer architecture**, trained on the **Tiny Shakespeare** dataset for simplicity, but you can extend it to larger datasets once you have the basic framework.

---

### **1. Setting Up the Transformer Model**

Let’s first set up the **Transformer model**. We’ll build a class that uses the components we discussed earlier (embedding layer, positional encoding, self-attention, and feedforward layers).

```python
import torch
import torch.nn as nn

class TransformerModel(nn.Module):
    def __init__(self, vocab_size, embed_size, num_heads, num_layers, dropout=0.2):
        super(TransformerModel, self).__init__()
        
        # Embedding for input tokens
        self.embedding = nn.Embedding(vocab_size, embed_size)
        
        # Positional Encoding to track token positions
        self.positional_encoding = PositionalEncoding(embed_size, dropout)
        
        # Transformer layers: Stacking multiple decoder layers
        self.layers = nn.TransformerDecoderLayer(embed_size, num_heads, dim_feedforward=2048, dropout=dropout)
        self.transformer_decoder = nn.TransformerDecoder(self.layers, num_layers)
        
        # Linear layer to project output to the vocab size
        self.fc_out = nn.Linear(embed_size, vocab_size)

    def forward(self, src, tgt):
        # Embed the source and target sequences and apply positional encoding
        embed_src = self.embedding(src) + self.positional_encoding(src)
        embed_tgt = self.embedding(tgt) + self.positional_encoding(tgt)
        
        # Pass through transformer decoder
        output = self.transformer_decoder(embed_tgt, embed_src)
        
        # Project to vocabulary size (logits)
        return self.fc_out(output)

class PositionalEncoding(nn.Module):
    def __init__(self, embed_size, dropout, max_len=5000):
        super(PositionalEncoding, self).__init__()
        self.dropout = nn.Dropout(p=dropout)
        
        # Create a matrix of position encodings
        pe = torch.zeros(max_len, embed_size)
        position = torch.arange(0, max_len, dtype=torch.float).unsqueeze(1)
        div_term = torch.exp(torch.arange(0, embed_size, 2).float() * (-torch.log(torch.tensor(10000.0)) / embed_size))
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)
        pe = pe.unsqueeze(0).transpose(0, 1)
        self.register_buffer('pe', pe)

    def forward(self, x):
        # Add positional encoding to the embeddings
        x = x + self.pe[:x.size(0), :]
        return self.dropout(x)
```

This sets up the **Transformer model** with a basic architecture. Now, let’s move on to the next step: **loading and tokenizing the data**.

---

### **2. Data Preprocessing and Tokenization**

We’ll work with the **Tiny Shakespeare** dataset, which is a compact text file containing Shakespeare's works. We need to tokenize the data, convert it to numerical representations, and split it into input-output sequences for training.

```python
import torch
from torch.utils.data import Dataset, DataLoader

# Load Tiny Shakespeare dataset
def load_data(file_path):
    with open(file_path, 'r') as f:
        text = f.read()
    return text

# Tokenizer: Convert characters to indices and vice versa
def create_tokenizer(text):
    chars = sorted(list(set(text)))
    vocab_size = len(chars)
    char_to_int = {ch: i for i, ch in enumerate(chars)}
    int_to_char = {i: ch for i, ch in enumerate(chars)}
    return char_to_int, int_to_char, vocab_size

# Custom dataset to create input/target pairs
class ShakespeareDataset(Dataset):
    def __init__(self, text, char_to_int, seq_length=100):
        self.text = text
        self.char_to_int = char_to_int
        self.seq_length = seq_length

    def __len__(self):
        return len(self.text) - self.seq_length

    def __getitem__(self, idx):
        input_seq = [self.char_to_int[ch] for ch in self.text[idx:idx + self.seq_length]]
        target_seq = [self.char_to_int[ch] for ch in self.text[idx + 1:idx + self.seq_length + 1]]
        return torch.tensor(input_seq, dtype=torch.long), torch.tensor(target_seq, dtype=torch.long)

# Load the data and create tokenizer
file_path = 'tiny_shakespeare.txt'
text_data = load_data(file_path)
char_to_int, int_to_char, vocab_size = create_tokenizer(text_data)

# Create Dataset and DataLoader
seq_length = 100
dataset = ShakespeareDataset(text_data, char_to_int, seq_length)
dataloader = DataLoader(dataset, batch_size=64, shuffle=True)
```

Here’s what’s happening:

- We load the **Tiny Shakespeare dataset** from a file and tokenize the characters into numerical indices.
- We create a custom **ShakespeareDataset** class that splits the text into sequences of **100 characters** and shifts them to create input and target pairs.
- We use **DataLoader** to batch the data, making it easier to feed the model during training.

---

### **3. Training the Transformer Model**

Now, we’ll define the **training loop**, where the model learns to predict the next character in the sequence. We’ll use **cross-entropy loss** to measure how well the model’s predictions match the actual target sequences.

```python
import torch.optim as optim

# Initialize model, optimizer, and loss function
embed_size = 512
num_heads = 8
num_layers = 6
dropout = 0.2

model = TransformerModel(vocab_size, embed_size, num_heads, num_layers, dropout)
optimizer = optim.Adam(model.parameters(), lr=0.001)
criterion = nn.CrossEntropyLoss()

# Training loop
num_epochs = 50
for epoch in range(num_epochs):
    model.train()
    epoch_loss = 0
    
    for input_seq, target_seq in dataloader:
        optimizer.zero_grad()
        
        # Forward pass
        output = model(input_seq, input_seq)
        loss = criterion(output.view(-1, vocab_size), target_seq.view(-1))
        
        # Backward pass and optimization
        loss.backward()
        optimizer.step()
        
        epoch_loss += loss.item()

    print(f"Epoch {epoch+1}/{num_epochs}, Loss: {epoch_loss/len(dataloader):.4f}")
```

### **Explanation:**
1. **Model Initialization**: We initialize our **TransformerModel** with `embed_size`, `num_heads`, and `num_layers`. The optimizer is **Adam**, and the loss function is **CrossEntropyLoss**.
2. **Training Loop**: For each epoch, we:
   - Perform a forward pass through the model.
   - Calculate the loss between the predicted and target sequences.
   - Perform a backward pass to update the weights.
   - Print the average loss for the epoch.

---

### **4. Generating Text from the Model**

Once the model is trained, we can generate text using the trained model by feeding it a starting sequence and having it predict the next characters.

```python
def generate_text(model, start_sequence, char_to_int, int_to_char, length=100):
    model.eval()
    input_seq = torch.tensor([char_to_int[ch] for ch in start_sequence], dtype=torch.long).unsqueeze(0)
    
    generated_text = start_sequence
    for _ in range(length):
        with torch.no_grad():
            output = model(input_seq, input_seq)
            next_char_idx = torch.argmax(output[0, -1]).item()
            next_char = int_to_char[next_char_idx]
            generated_text += next_char
            input_seq = torch.cat([input_seq, torch.tensor([[next_char_idx]], dtype=torch.long)], dim=1)
    
    return generated_text

# Generate text
start_sequence = "O Romeo"
generated_text = generate_text(model, start_sequence, char_to_int, int_to_char, length=500)
print(generated_text)
```

### **Explanation:**

1. **Text Generation**: We provide a starting sequence (e.g., `"O Romeo"`) and generate characters one by one using the trained model.
2. **Greedy Decoding**: We use `torch.argmax` to pick the character with the highest probability after each step and append it to the generated sequence.
3. **Loop**: The process is repeated until the desired text length is reached.

---

###

 **5. Saving and Loading the Model**

To avoid retraining the model every time, we’ll save the model’s weights and load them for future use.

```python
# Save the trained model
torch.save(model.state_dict(), 'transformer_model.pth')

# Load the saved model
model.load_state_dict(torch.load('transformer_model.pth'))
model.eval()
```

### **Explanation:**

1. **Saving**: We use `torch.save()` to save the model's state dictionary (weights) to a file.
2. **Loading**: When needed, we load the model using `torch.load()` and call `model.eval()` to set the model to evaluation mode (which disables dropout).

---

### **Putting It All Together**

Here’s a summary of what we’ve done:

1. **Defined the Transformer model** with embeddings, positional encodings, self-attention, and feedforward layers.
2. **Tokenized the data** and prepared it for training using **DataLoader**.
3. **Trained the model** using **cross-entropy loss** and **Adam optimizer**.
4. **Generated text** by feeding a starting sequence and having the model predict the next characters.
5. **Saved and loaded** the trained model for future use.

With this complete pipeline, you now have a fully functioning **LLM**. While we used the **Tiny Shakespeare** dataset for simplicity, this framework can be applied to **much larger datasets** with minimal adjustments.

---

### **Conclusion**

Congratulations! 🎉 You've now successfully built a **Large Language Model** using the **Transformer architecture** from scratch. You learned how to train the model, generate text, and save/load it for future use. This framework can be extended for **real-world applications** like chatbots, text generation systems, and more sophisticated NLP tasks.

Next steps? You can scale this model to larger datasets, experiment with different architectures, or fine-tune a pretrained model like GPT for even more powerful performance. 🚀
