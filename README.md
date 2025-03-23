# EC2-Using-Terraform
We are going to try & learn the automation of provisioning of EC2 instances from AWS using Terraform.
Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp. It helps us to define, provision, and manage cloud infrastructure using a declarative configuration language called HashiCorp Configuration Language (HCL).
It follows a declaractive approach, meaning we define the desired state of our infrastructure, and Terraform ensures that the actual infrastructure matches that state.
Terraform's workflow follows three major steps : 
- Code: we define infrastructure resources using a .tf file, this step is generally called writing configuration.
- Plan: Terrform compares the inital state and the final state and shows what changes it's going to make 
- Apply: Terraform provisions or updates resources based on the configuration to reach the final state
---

## Step 1 : Installing Terraform
- First we need to make sure that we have gnupg, software-properties-common, and curl packages installed using the below command
  - gnupg: Since gnupg is used for verifying software packages and managing keys, it's needed when adding third-party repositories (like Docker, Node.js, etc.), as they require GPG key verification.
  - software-properties-common: needed for using the add-apt-repository(allows users to easily add Personal Package Archives (PPAs) and other repositories) command to manage software sources.
 ```
 sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
 ```

- Now we install the Hashicorp GPG key
  - A GPG (GNU Privacy Guard) key is a cryptographic key used to verify the authenticity of a software source. It          ensures that packages from a repository have not been tampered with and come from a trusted source.
```
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```
 - using `wget` we download the gpg key from hashicorp's website, using `-0-` we ensure it doesn't get saved in a file in current working directory, as we need to store process it into binary before storing it in correct directory for later use
 - we convert the output recieved from wget by passing it to `gpg --dearmor` as input using pipe(|) commnad into binary format required by apt
 - using `tee` we write the input to the specified file location while `> /dev/null` supresses the output to keep the terminal clean

- Verify the gpg key's fingerprint to ensure it's integrity and authenticity
  - A GPG fingerprint is a unique identifier for a GPG key. It is a 40-character hexadecimal string that helps verify the authenticity of a key.
```
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
```
 - 1st we we prevent gpg from using default keyring(it is a file that stores public and private GPG keys allowing the system to manage, store, and verify cryptographic keys securely) and then specify the exact keyring file to use and `--fingerprint` displays the fingerprint of the key in the specified keyring

- Now we add the official HashiCorp APT repository to our system so that we can install packages like Terraform, Vault, Consul, Packer, and Nomad using apt.
```
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```
