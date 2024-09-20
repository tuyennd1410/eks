# 00 - 🥰 summary

1. Install AWS cli
2. Configure AWS Credentials
3. Install eksctl & kubectl cli

# 01 - 👷🏻‍♂️ install aws cli

📗 Ref 1:
[https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

📗 Ref 2:
[https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

MacOs:

```
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

### 🚓 verify aws cli installed location

💻 Command

```
which aws
```

Output Example:

```
/usr/local/bin/aws
```

🩵 command return a location of aws cli is GOOD

### ✅ verify aws version

💻 Command

```
aws --version
```

Output Example:

```
aws-cli/2.9.7 Python/3.9.11 Darwin/23.4.0 exe/x86_64 prompt/off
```

🩵 aws cli version 2.x is GOOD

# 02 - 🧑🏻‍💻 configure aws credentials for aws cli

### 1️⃣ Generate AWS Credentials from your AWS Account

1. Go to **AWS Management Console**
2. Go to **AWS IAM** Service Dashboard
3. Click on **Users**
4. Click button **Create user**
5. Enter your username. For example: _vietaws_
6. Click **Next**
7. Select Permission options, choose the last option **Attach polices directly**
8. Select **AdministratorAccess**. ⚠️ Important note: This is for quick setup
   for learning purpose, for production we recommend to follow least privilege.
9. Click **Next** and **Create user**

### 2️⃣ We have created user successfully! Now, we can get the credentials

1. Click on user that you created. For example: _vietaws_
2. Click on **Security Credentials** tab
3. Scroll down to **Access keys** section
4. Click on **Create access key**
5. Choose **Command line interface (CLI)**
6. Tick ☑ _I understand the above recommendation and want to proceed to create
   an access key._
7. Click **Next** and **Create access key**
8. In next window, you will get **Access Key** & **Secret Key**. Don't share
   your key to anyone or they can access your AWS account.

### 3️⃣ Configure aws cli

Open your terminal and enter below command

```
aws configure
AWS Access Key ID [None]: JGILL6889GHJFHKKL  (Replace your creds - access key)
AWS Secret Access Key [None]: JGILL6889GHJJGILL6889GHJJGILL6889GHJ  (Replace your creds - secret key)
Default region name [None]: ap-southeast-1
Default output format [None]: json
```

### 4️⃣ verify aws configure credential setup

command

```
aws sts get-caller-identity
```

Output example:

```
{
    "UserId": "JGILL6889GHJ",
    "Account": "957323120273",
    "Arn": "arn:aws:iam::825770460273:user/vietaws"
}
```

# 03 - install eksctl & kubectl cli

Guide: https://eksctl.io/installation/

```
# for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
ARCH=amd64
PLATFORM=windows_$ARCH

curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.zip"

# (Optional) Verify checksum
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

unzip eksctl_$PLATFORM.zip -d $HOME/bin

rm eksctl_$PLATFORM.zip
```

### ✅ verify

🌻 command 1:

```
eksctl info
```

Output example:

```
eksctl version: 0.175.0
kubectl version: v1.29.4
OS: darwin
```

🌻 command 2

```
kubectl version
```

Output example:

```
Client Version: v1.29.1
Kustomize Version: v5.0.4-0.20230601165947-vietaws92834928
```

🚀🚀🚀 Congratulation!
