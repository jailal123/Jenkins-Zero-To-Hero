# Jenkins-Zero-To-Hero

Are you looking forward to learn Jenkins right from Zero(installation) to Hero(Build end to end pipelines)? then you are at the right place. 

## Installation on EC2 Instance

YouTube Video ->
https://www.youtube.com/watch?v=zZfhAXfBvVA&list=RDCMUCnnQ3ybuyFdzvgv2Ky5jnAA&index=1


![Screenshot 2023-02-01 at 5 46 14 PM](https://user-images.githubusercontent.com/43399466/216040281-6c8b89c3-8c22-4620-ad1c-8edd78eb31ae.png)

Install Jenkins, configure Docker as agent, set up cicd, deploy applications to k8s and much more.

## AWS EC2 Instance

- Go to AWS Console
- Instances(running)
- Launch instances

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

### Install Jenkins.

Pre-Requisites:
 - Java (JDK)

### Run the below commands to install Java and Jenkins

Install Java

```
sudo apt update
sudo apt install openjdk-17-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080    [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.
 Since your Windows Downloads folder is located at:

makefile
Copy code
C:\Users\dell\Downloads
You can access it from WSL using:

bash
Copy code
cd /mnt/c/Users/dell/Downloads
✅ This works because:

C:\ in Windows = /mnt/c/ in WSL

Backslashes (\) in Windows = forward slashes (/) in Linux

to login to ec2 via local ubuntu switch to root user 

You're getting two main errors while trying to SSH into your EC2 instance from **WSL**. Let's fix them one by one:

---

### 🧨 1. **SSH Key File Permissions Too Open**

```
Permissions 0777 for 'jailal.pem' are too open.
```

🔧 **Fix: Restrict file permissions**
In WSL, go to the folder where `jailal.pem` is (e.g., Downloads), then run:

```bash
chmod 400 jailal.pem
```

This sets secure permissions required by SSH (readable only by you).

---

### ⚠️ 2. **.ssh Directory Does Not Exist**

```
Could not create directory '/home/jailal/.ssh' (No such file or directory).
```

🔧 **Fix: Create the `.ssh` directory manually**

```bash
mkdir -p ~/.ssh
```

Then make sure it has proper permissions:

```bash
chmod 700 ~/.ssh
```

---

### ✅ Now Try Again:

```bash
ssh -i jailal.pem ubuntu@52.87.213.222
```

---

### 📝 Optional: Move your `.pem` to `.ssh` for cleanliness

```bash
**mv jailal.pem ~/.ssh/
chmod 400 ~/.ssh/jailal.pem
ssh -i ~/.ssh/jailal.pem ubuntu@52.87.213.222**
```

Great! 🙌 Now that you've found your `jailal.pem` file, you can continue with the SSH connection.

---

### ✅ Make Sure Permissions Are Correct

```bash
chmod 400 ~/.ssh/jailal.pem
```

---

### 🔐 Then Connect to the EC2 Instance

```bash
ssh -i ~/.ssh/jailal.pem ubuntu@52.87.213.222
```

> 🔁 Replace `ubuntu` if your EC2 instance uses a different username (like `ec2-user` for Amazon Linux, `admin` for some distros).

---

### 💡 Optional Tip – Avoid Typing Key Every Time

You can create an SSH config to simplify it:

```bash
nano ~/.ssh/config
```

Add this:

```ini
Host my-ec2
    HostName 52.87.213.222
    User ubuntu
    IdentityFile ~/.ssh/jailal.pem
```

Save and exit (Ctrl+O, Enter, Ctrl+X), then connect like this:

```bash
ssh my-ec2
```

Let me know if you want to copy files to/from EC2 or deploy anything there!

