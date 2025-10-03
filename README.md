# **HOW TO STORE FILES ON IRYS USING BASE_ETH MAINNET AS PAYMENT (MAINNET INTERACTION)**
This is a step by step guide on how you can store files permantly on irys using baseEth as payment, this will count as irys mainnet interaction because the stored files will be uploaded to irys mainnet once mainnet goes live!

#  **REQUIREMENTS**
1. vs code for coding environment
2. a spare wallet with some amount of BaseEth (less than a dollar)
3. internet connection

# **STEPS** 
1. create a folder titled "irys saver" or use any preferred name on your pc (could be your desktop)
2. open vs code and locate the created folder, open it.
3. make sure the file(image in this case) is saved in your projects file directory and named with .png, .js etc, muine is nameded as  irys.png in this case
4. copy and save your image in your project folder, name it as irys.png (skip if you understand step 3)
5. on your coding environment , press "CTRL + ~" to open the command line
#  Install dependencies
```bash
npm init -y
npm install @irys/upload @irys/upload-ethereum
```
# **Set up environment variables**
Create a .env file: (change your_wallet_private_key to your private key)
```env
# WARNING: NEVER commit this file to GitHub or share it.
PRIVATE_KEY="YOUR_BASE_MAINNET_WALLET_PRIVATE_KEY_HERE"
```
# **Write the Code (index.js)**
Create a new file named index.js.

Copy and paste the entire code below. This script connects to the Base Mainnet, funds your account, and uploads a file.
```index.js
// index.js

import { Uploader } from "@irys/upload";
import { BaseEth } from "@irys/upload-ethereum"; // 🚀 CORRECT IMPORT: Use 'BaseEth'
import 'dotenv/config'; 
import * as fs from 'fs'; 

// ⚠️ Ensure your .env file has a PRIVATE_KEY variable set.

// --- STEP 1: Irys Connection for Base Mainnet ---
const getIrysUploader = async () => {
    // ⬇️ Use BaseEth here
    const irysUploader = await Uploader(BaseEth).withWallet(process.env.PRIVATE_KEY);
    return irysUploader;
};
// --- STEP 2: Fund the Irys Account (If needed) ---
const fundAccount = async (irysUploader) => {
    // We are requesting a fund amount of 0.0001 tokens (Base ETH).
    const fundAmount = irysUploader.utils.toAtomic(0.00000000001); 
    
    console.log(`Attempting to fund Irys account with ${irysUploader.utils.fromAtomic(fundAmount)} ${irysUploader.token}...`);

    try {
        const balance = await irysUploader.getBalance();
        console.log(`Current Irys balance: ${irysUploader.utils.fromAtomic(balance)} ${irysUploader.token}`);
        
        // Only fund if the balance is too low
        if (balance < fundAmount) {
             const fundTx = await irysUploader.fund(fundAmount);
             console.log(`✅ Successfully funded! Transaction ID: ${fundTx.id}`);
        } else {
             console.log("✅ Irys account is already funded. Skipping.");
        }
    } catch (e) {
        console.error("❌ Error when funding: Ensure your wallet has ETH on Base Mainnet and the private key is correct.", e);
        throw e;
    }
};

// --- STEP 3: Upload the File ---
const uploadFile = async (irysUploader) => {
   
    const fileName = "irys.png";
   // fs.writeFileSync(fileName, "Hello Irys on Base Mainnet! This data is permanent.");
    
    // Define optional tags
    const tags = [
        { name: "Content-Type", value: "application/json" },
        { name: "App-Name", value: "MyBaseApp" }
    ];
    
    console.log(`\nAttempting to upload file: ${fileName}`);
    
    try {
        const receipt = await irysUploader.uploadFile(fileName, { tags: tags });
        
        console.log("-----------------------------------------");
        console.log(`✅ File uploaded successfully!`);
        console.log(`Permanent URL: https://gateway.irys.xyz/${receipt.id}`);
        console.log("-----------------------------------------");
        
    } catch (e) {
        console.error("❌ Error when uploading file. Check your funding status and network connection.", e);
    }
};


async function main() {
    try {
        const irysUploader = await getIrysUploader();
        
        // 1. Fund the account first (required for paid uploads)
        await fundAccount(irysUploader);
        
        // 2. Then upload the file
        await uploadFile(irysUploader);
        
    } catch (error) {
        console.error("An error occurred during setup or execution:", error.message);
    }
}

main();
```
# **Setting up package.json**
Go to your package.json file and make sure it looks like this:

```package.json
{
  "name": "irys-saver",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "type": "module",  
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```
# **Execution**
Make sure you are in the terminal tab in VS Code.
Run the script using the following command:
```Bash
node index.js
```
Expected Output
If successful, the script will first check your balance (and fund if needed) and then print the permanent gateway URL for your file:
```Bash
Attempting to fund Irys account with 1e-11 base-eth...
Current Irys balance: 0.0001 base-eth
✅ Irys account is already funded. Skipping.

Attempting to upload file: irys.png
-----------------------------------------
✅ File uploaded successfully!
Permanent URL: https://gateway.irys.xyz/ATaBmyBUf7z7atmYqdpnudHMqUD7NYEgpDfFjMpJzL8q
-----------------------------------------
```
**HOW TO VIEW THE STORED IMAGE:**
once the script runs succesfully, you'ii be given a permanent url;
1. open the link in any browser , you will see raw file
2. save the raw file with .png format
3. once done, you will be able to view the image

**THANK YOU, IF YOU HAVE ANY QUESTIONS... DON'T HESITATE TO SHOOT A DM (@LAE_GEND)**
