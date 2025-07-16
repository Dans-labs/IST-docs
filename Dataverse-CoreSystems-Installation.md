# Dataverse (Core Systems) Installation

Following the [README](https://github.com/DANS-KNAW/dans-core-systems/blob/master/README.md) of https://github.com/DANS-KNAW/dans-core-systems/

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

* `pin install ansible-core==2.16.14 PyYAML requests`


On 4.Test that you can start the default base boxes: 

* `python ./scripts/start-preprovisioned-box.py`

```yaml
preprovisioned:
  default_servers:
    - dev_vocabs
  ` - dev_archaeology
```




## Vagrant

Vagrant boxes status: `vagrant global-status --prune`


**box domains:**
* 192.168.56.38   dev.archaeology.datastations.nl vagrant-name: dev_archaeology
* 192.168.56.42   dev.vocabs.datastations.nl vagrant-name: dev_vocabs


SSH to vagrant box: `vagrant ssh dev_vocab`


# Questions
* How are the boxes used for development?