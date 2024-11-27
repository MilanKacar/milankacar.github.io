---
layout: post  
title: "#89 🔗 Connect Bitbucket to Dataform 🚀🧠"
categories: [Tutorial, Programming]
difficulty: Easy
tags: [GCP]
---

Are you ready to streamline your data workflows by connecting **Bitbucket** with **Dataform**? Let's dive into this guide and make your data integration journey smoother than ever. 🚀  

Dataform empowers you to orchestrate and manage your SQL-based workflows, and by linking it with Bitbucket, you can version-control your data projects like a pro! 💾 But here's the catch: **Dataform only supports SSH for connecting with Bitbucket repositories**. No worries—we'll cover everything you need to set this up step by step. 😊  

---

## 🛠️ **Prerequisites**  
Before we dive into the setup, make sure you have the following ready:  

1. A **Bitbucket** repository to store your Dataform project.  
2. A **Dataform account** with an active workspace.  
3. An SSH key pair (public and private keys).  
4. A basic understanding of Git.  

---

## 📋 **Step 1: Generate SSH Keys**  
To connect Dataform to Bitbucket via SSH, you need an SSH key pair.  

### 🔑 **Generating an SSH Key Pair**  
1. Open a terminal (Linux/Mac) or Git Bash (Windows).  
2. Run the following command to generate your SSH key pair:  

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```  
   - Save the key in the default location (press Enter when prompted).  
   - Provide a passphrase for additional security (optional).  

3. Your SSH key pair will be generated at:  
   - **Public key**: `~/.ssh/id_rsa.pub`  
   - **Private key**: `~/.ssh/id_rsa`  

---

## 🔗 **Step 2: Add Your SSH Key to Bitbucket**  
Now that you have your SSH keys, let's add the public key to Bitbucket.  

1. **Log in to your Bitbucket account**.  
2. Navigate to **Personal Settings** > **SSH Keys**.  
3. Click **Add Key**.  
4. Paste the content of your public key (`id_rsa.pub`) into the text field.  

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```  

5. Give your key a recognizable label (e.g., "Dataform Integration") and save it.  

---

## 🖥️ **Step 3: Configure Your Dataform Project**  
Now, let's connect your Bitbucket repository with your Dataform workspace.  

### **Add the SSH Key to Dataform**  
1. Create Repository in your **Dataform** account.  
2. Go to your repository.  
3. Under the **Settings** section, choose **CONNECT WITH GIT**.
4. Choose SSH
5. Put your SSH link from created repository (from bitbucket) to the field `Remote Git repository URL` - e.g. ` git@bitbucket.org:<my_project>/dataform-production.git`
6. In new tab in GCP - go to `Secret Manager` and there go to `CREATE SECRET` 
7. Upload your **private SSH key** (`id_rsa`) you generated earlier.  

   > ⚠️ **Important:** Ensure the private key file does not have a passphrase. If it does, remove it using:  
   > ```bash
   > ssh-keygen -p -f ~/.ssh/id_rsa
   > ```  
8. Go back to your `DataForm` tab in your browser after you uploaded your key. Choose your key from `Secret` drop down menu.
9. Open your terminal and type
 ```bash
 ssh-keyscan bitbucket.org
 ```  
11. For the public host key value put the value you got - currently it is 
```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDQeJzhupRu0u0cdegZIa8e86EG2qOCsIsD1Xw0xSeiPDlCr7kq97NLmMbpKTX6Esc30NuoqEEHCuc7yWtwp8dI76EEEB1VqY9QJq6vk+aySyboD5QF61I/1WeTwu+deCbgKMGbUijeXhtfbxSxm6JwGrXrhBdofTsbKRUsrN1WoNgUa8uqN1Vx6WAJw1JHPhglEGGHea6QICwJOAr/6mrui/oB7pkaWKHj3z7d1IC4KWLtY47elvjbaTlkN04Kc/5LFEirorGYVbt15kAUlqGM65pk6ZBxtaO3+30LVlORZkxOh+LKL/BvbZ/iRNhItLqNyieoQj/uh/7Iv4uyH/cV/0b4WDSd3DptigWq84lJubb9t/DnZlrJazxyDCulTmKdOR7vs9gMTo+uoIrPSb8ScTtvw65+odKAlBj59dhnVp9zd7QUojOpXlL62Aw56U4oO+FALuevvMjiWeavKhJqlR7i5n9srYcrNV7ttmDw7kf/97P5zauIhxcjX+xHv4M=
```

12. You are done!
![Dataform connected to the bitbucket]({{ site.baseurl }}/images/dataform_bitbucket.png)
---

## 🎉 **Conclusion**  
Congratulations! 🎊 You've successfully connected Bitbucket with Dataform using SSH. With this setup, you can now manage your Dataform projects like a Git pro—track changes, collaborate efficiently, and automate workflows.  

Happy coding! 💻✨  

If you have any questions or face any hiccups, drop a comment below. Let's build something amazing together! 🌟

