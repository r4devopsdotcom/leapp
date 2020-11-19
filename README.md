Leapp
=========
[![Github All Releases](https://img.shields.io/github/downloads/noovolari/leapp/latest/total)](https://github.com/Noovolari/leapp/releases/latest)
- Website: https://www.leapp.cloud/
- Roadmap: [Roadmap](https://github.com/Noovolari/leapp/projects/1)
- Tutorials: [Tutorials](#tutorials)
- Chat with us: [Discord](https://discord.gg/wHh2kyK)

![logo](.github/images/README-1.png)

Leapp is your everyday companion to access your cloud; designed to work with Cloud Providers APIs, CLIs, and SDKs.
It's a software that securely stores your access information and generates temporary credential sets to access your cloud ecosystem from your local machine.
**For example, while using the AWS CLI it may become annoying to switch to a different profile or use the --profile argument before issuing every command.**
Leapp lets you have a new set of credentials and give access to that account with a click.
Leapp also will manage Federated access through Identity Provides.
![Leapp App](.github/images/Leapp-Keynote-pitch.001.png)
# Key features
- **Switch account with a click**: collect all your cloud accounts access data in a single place and connect straight away. Leverage cloud RBAC to impersonate your roles in a click, and don't waste time manually manage or edit your credentials file.
- **Straight programmatic access via SSO**: leverage your company identity to access your environment through federated single sign-on. No more credentials management. Leapp allows you to get to your cloud resources with your company email and password. Scroll down for our [supported use cases](#supported-cloud-providers).
- **Clean credentials file**: ever wondered what someone can do by stealing your credentials file? We got you covered. We erase these files when you close Leapp and regenerate them when you open it! Leave your desk and get a coffee at ease.
- **Automatic credentials management**: move freely across your multi-cloud environment as Leapp automatically manages your access credentials for you. It handles temporary keys generation, rotation, and auto-renew to comply with security best-practices.
- **Ease federation with truster accounts**: federating each account is a pain so why don't use truster accounts to grant access easier and painlessly? We use your federated role as a gateway to all trusted roles in all other accounts! Seems great, isn't it? See our [documentation](https://github.com/Noovolari/leapp/blob/master/.github/GLOSSARY.md#trusting) to get more info.
- **MFA support enabled**: access AWS users and services even when they have MFA device enabled on AWS console.
- **HTTP/HTTPS in-app proxy configuration:** allow Leapp to forward in-app HTTP/HTTPS traffic to the specified Proxy Server.


![Leapp App animation](.github/images/Leapp-animation.gif)

## Supported Cloud Providers
- **AWS** - :white_check_mark:
- **AZURE** - :white_check_mark:
- **GCP** - :soon:
## Supported Identity Providers
- **G Suite to AWS** - :white_check_mark:
- **G Suite to Azure** - :white_check_mark:
- **AZURE AD to Azure** - :white_check_mark:
- **AZURE AD to AWS** - :soon:
- **AWS SSO** - :soon:
# Installation
Get [here](https://github.com/Noovolari/leapp/releases/latest) the latest release.
To install the compiled version, choose the one for your **OS** and simply **double-click** on the executable.
# Use Cases
Our use cases are hereby presented to give you a hint on how Leapp can be of help depending on the type of setup 
you have in your company and what kind of credentials you need to get.

#### AWS Plain Access
Store AWS IAM User's Access Keys in your System Vault through Leapp. 
Leapp automatically manages **Access Key ID** and **Secret Access Key** in your AWS credentials, 
generating temporary credentials form them.

**No credentials** are stored in Leapp. 

Please see [Vault strategy](#vault-strategy) for more information.

![Plain Access Usecase](.github/images/plain-gif.gif)

See setup [tutorial](.github/tutorials/TUTORIALS.md)

*Note: it's possible to assign a MFA device to a plain session. Please see [MFA section](#multi-factor-authentication) for more details.*

#### AWS Federated Access
Leverage company identity to access environment through federated single sign-on. 
Federation is established between **G Suite** and **AWS**. No more AWS credentials 
management is needed. Leapp allows to get to cloud resources with company email and password.

![Federated Access Usecase](.github/images/federated-gif.gif)

See setup [tutorial](.github/tutorials/TUTORIALS.md)

#### AWS Truster Access
Federating each account is difficult so use truster accounts to grant access easier and painlessly.
We use federated role as a gateway to all trusted roles in all other AWS accounts.
In this access strategy a **truster role** is assumed by a **federated role**.

![Truster Access Usecase](.github/images/truster-gif.gif)

See setup [tutorial](.github/tutorials/TUTORIALS.md)

*Note: it's possible to apply MFA to a truster session by setting it on the plain account it relies on. Please see [MFA section](#multi-factor-authentication) for more details.*

#### Azure Access
Use Leapp to do Single Sign On with G Suite on Azure to get access to your 
**Subscriptions**. In this use case is **mandatory** to have defined a Federation 
between Google and Azure. Leapp manage the login process for you to have Azure CLI 
ready to be used.

![Azure Access Usecase](.github/images/azure-gif.gif)

See setup [tutorial](.github/tutorials/TUTORIALS.md)

# Rotating Credentials
Leapp is created with security in mind: that is, **NO** credentials are saved in our system whatsoever. 
Nor in code neither in our configuration file. Everytime a credential is generated 
is **temporary**, and **no long-term ones are ever saved** in plain accessible files or locations.

# Vault Strategy
On Leapp we use [Keytar](https://github.com/atom/node-keytar) as a library to maintain a secure vault for sensible information. 
We use it wrapping the functions listed below.

### Prerequisite for using on Linux systems
Currently this library uses `libsecret` so you **may** need to install it before running Leapp.
Depending on your distribution, you will need to run the following command:
* Debian/Ubuntu: `sudo apt-get install libsecret-1-dev`
* Red Hat-based: `sudo yum install libsecret-devel`
* Arch Linux: `sudo pacman -S libsecret`

### Supported versions for contributors
Each release of `keytar` includes prebuilt binaries for the versions of Node and Electron that are actively supported by our project. 
Please refer to the release documentation for [Node](https://github.com/nodejs/Release) and [Electron](https://electronjs.org/docs/tutorial/support) 
to see what is supported currently.

## Keytar documentation
Every function in `keytar` is **asynchronous** and returns a **Promise**. The promise will be rejected with any error that occurs or 
will be resolved within the function's `yields` value. 
In Leapp we use the three methods listed below to save and store sensible information and to **avoid** keeping them anywhere in the code. 
The delete function is used when an **AWS Plain** session is **updated** or **removed** from the list.

### getPassword(service, account)
Get the stored password for the `service` and `account`.
`service` - The string service name.
`account` - The string account name.
Yields the string password or `null` if an entry for the given service and account was not found.

### setPassword(service, account, password)
Save the `password` for the `service` and `account` to the keychain. Adds a new entry if necessary, or updates an existing entry if one exists.
`service` - The string service name.
`account` - The string account name.
`password` - The string password.
Yields nothing.

### deletePassword(service, account)
Delete the stored password for the `service` and `account`.
`service` - The string service name.
`account` - The string account name.
Yields `true` if a password was deleted, or `false` if an entry with the given service and account was not found.

## How can I find Leapp data in the Vault?
Every key stored by Leapp in the vault is named **Leapp**. The account name shows the description of the element saved by our software.

## How Keytar translate to system vaults?
Keytar translate to the following system vaults depending on the OS:
* On **macOS** the passwords are managed by the *Keychain*. 
* On **Linux** they are managed by the Secret Service *API/libsecret*. 
* On **Windows** they are managed by *Credential Vault*.

# Multi-Factor Authentication
Leapp support Multi-Factor Authentication for **AWS Plain and Truster** access strategies. 
The system will ask for the **MFA device ARN** provided by AWS when creating/editing 
a Plain session, which corresponds to an **AWS IAM User**.

## Setup MFA in AWS
To enable MFA for a specific IAM User, you need to assign an MFA device to it. Move to 
the Security Credentials tab within the IAM User's Summary. 

![mfa-ex-1](.github/images/mfa-ex-1.png)

Once there, you should be able to find the *Assigned MFA device* configuration, from which a new MFA device can
be assigned to the user.
MFA protects access to your AWS Account prompting for a token when you try to start the
Leapp session.

### MFA restricted access
**MFA device ARN** is *necessary*, not *sufficient*, condition to protect access to your cloud resources. 
User or Apps can do operations on cloud resources without being prompted for an MFA code. To enforce MFA 
for specific actions described in a Role's policy, a `"Condition": {"Bool": {"aws:MultiFactorAuthPresent": "true"}}` 
statement must be present for action `"Action": "sts:AssumeRole"` in the role policy.

## Setup MFA in Leapp
To assign an MFA device to a **plain account** just insert a *physical device's serial number* or an *MFA device ARN* in 
the corresponding field of plain strategy's form. The device is then associated with that specific session, and the **MFA token** will be prompted
when the session starts.

*Note 1: if you need to remove MFA from a plain session, just edit it and leave the field blank.*
*Note 2: when you setup a truster account from a plain session with MFA enabled, **Leapp** inherently associates MFA checks to it.*

## About MFA and Session durations
There are 2 **environment variables** contained in Leapp which defines how a session is managed:
* *session duration*: defines **how often** the system will refresh your **temporary** credentials. [defaults to 1200s / 20min]
* *session token duration* defines **how long** the **session token** will be used for each *session duration* refresh action. [defaults to 36000s / 10h] 

*Note 1: because Amazon doesn't allow to set MFA token expiration for more than 1h for *
(inserire una nota che parla dell'utilizzo del session token per MFA)
(spiegare che le credenziali che durano 10h sono salvate nel vault, sicure, ed utilizzate 
puntualmente per generare nuove credenziali associate all'account truster; queste ultime vengono 
scritte nel file di credenziali, non vengono salvate nel vault)

# HTTP/HTTPS in-app proxy
Leapp allows for HTTP/HTTPS protocols, specifying a proxy server to which the in-app requests are sent. Both authenticated and non authenticated proxy are supported. In the option panel you can configure protocol, url, port, and authentication information. See image below
![image](.github/images/options-proxy.png)
### Note for Azure Sessions
Leapp uses Azure CLI to authenticate the **User** to retrieve the tokens for the session. This means that you **must** configure your Azure proxy settings locally to allow the Azure CLI to do ```az login``` properly, as Leapp is no responsible for that.
This extends generally to all CLIs and external tools that need to communicate over Internet behind a proxy configuration.
# Logs
By default, Leapp writes logs to the following locations:
- on Linux: ~/.config/**Leapp**/logs/log.log
- on macOS: ~/Library/Logs/**Leapp**/log.log
- on Windows: %USERPROFILE%\\AppData\\Roaming\\**Leapp**\\logs\\log.log
Logs are structured in the following way:
```
[YYYY-MM-DD HH:mm:ss.mmm] [LEVEL] [rendered/system] [COMPONENT] MESSAGE {Useful Object / Stacktrace Err Object}
```
*Note: please always add logs whenever possible to any issue you want to fill to enable the team identify the problem quickly*
# Quick List
Quick list is the component in Leapp that helps you manage all your sessions
together. You'll probably pass most of your time here so this is a quick 
list tutorial on how it works!
- [Manage Quick List](.github/tutorials/MANAGE_QUICK_LIST.md)
## Links
- [Glossary](.github/GLOSSARY.md): find other information about the system
- [Roadmap](https://github.com/Noovolari/leapp/projects/1): view our next steps and stay up to date
- [Contributing](./.github/CONTRIBUTING.md): follow the guidelines if you'd like to contribute to the project
- [Project Structure](./.github/PROJECT_STRUCTURE.md): check how we structured the project and where to find the files
## License
[Mozilla Public License v2.0](https://github.com/Noovolari/leapp/blob/master/LICENSE)
