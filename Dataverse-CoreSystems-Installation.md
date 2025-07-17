hackpad URL: https://hackmd.io/@pwDvwP9iT26n_AGA3SL1fQ/H1hQPCEIgg/edit


# Dataverse (Core Systems) Installation <img src="https://emojis.slackmojis.com/emojis/images/1643514977/10031/60fps_parrot.gif" />


**Following the [README](https://github.com/DANS-KNAW/dans-core-systems/blob/master/README.md) of https://github.com/DANS-KNAW/dans-core-systems/**


| Tool       | Version                 | ICT-Support |
|------------|-------------------------|-------------|
| Git        | 2.34.1                  | Yes         |
| <s>Java</s>       | 17                      | No          |
| Vagrant    | 2.4.1                   | No          |
| Ansible    | 2.16.0                  | Yes         |   
| <s>Maven</s>      | 3.6.3 `(mvn --version)` | No          |
| VirtualBox | 7.0.x                   | No          |
| <s>RPM</s>        | 4.17.0                  | No          |
| <s>Jena</s>       | 5.1.0                   | Yes         |




In python venv:

* `pip install ansible-core==2.16.14 PyYAML requests`

<strong style="color:red">NOTICE</strong>: Virtualbox can have issue if the system is booted in secure mode.

On 4.Test that you can start the default base boxes: 

* `python ./scripts/start-preprovisioned-box.py`

```yaml
preprovisioned:
  default_servers:
    - dev_vocabs
  ` - dev_archaeology
```

## Virtual box


see https://github.com/DANS-KNAW/dans-core-systems/blob/master/docs/howto-setup-framework-laptop.md#install-virtual-box on the installation of virtualbox


### errors:
```
No usable default provider could be found for your system.

Vagrant relies on interactions with 3rd party systems, known as
"providers", to provide Vagrant with resources to run development
environments. Examples are VirtualBox, VMware, Hyper-V.

The easiest solution to this message is to install VirtualBox, which
is available for free on all major platforms.

If you believe you already have a provider available, make sure it
is properly installed and configured. You can see more details about
why a particular provider isn't working by forcing usage with
`vagrant up --provider=PROVIDER`, which should give you a more specific
error message for that particular provider.
```

`vagrant up --provider=virtualbox`


in `apt install virtualbox-7.0` Alessandra got the following error

```
The following packages have unmet dependencies:
 virtualbox-7.0 : Depends: libvpx7 (>= 1.10.0) but it is not installable
                  Recommends: libsdl-ttf2.0-0 but it is not going to be installed

```

fix:
`sudo apt --fix-broken install`


Jan:
```
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo gpg --dearmor --yes --output /usr/share/keyrings/oracle-virtualbox-2016.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] http://download.virtualbox.org/virtualbox/debian jammy contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
sudo apt update
sudo apt install virtualbox-7.0
sudo usermod -a -G vboxusers $USER
reboot
```    

Andre: deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.org/virtualbox/debian noble contrib
* ubuntu version has to change (`jammy` -> `noble`) in order to match the current OS version



## Vagrant

On Vagrant installation, see https://github.com/DANS-KNAW/dans-core-systems/blob/master/docs/howto-setup-framework-laptop.md#install-vagrant

Vagrant boxes status: `vagrant global-status --prune`


**box domains:**
* 192.168.56.38   dev.archaeology.datastations.nl vagrant-name: dev_archaeology
* 192.168.56.42   dev.vocabs.datastations.nl vagrant-name: dev_vocabs


SSH to vagrant box: `vagrant ssh dev_vocab`


# Questions
* How are the boxes used for development?
    * API calls (ie. data migration) testing
    * changes to webserver testing