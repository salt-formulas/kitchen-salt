
# Provisioner Options

key | default value | Notes
----|---------------|--------
salt_install| "bootstrap" | Method by which to install salt, "bootstrap" or "apt"
salt_bootstrap_url | "http://bootstrap.saltstack.org" | location of bootstrap script
salt_bootstrap_options | | optional options passed to the bootstrap script
salt_version | "0.16.2"| desired version, only affects apt installs
salt_apt_repo | "http://apt.mccartney.ie"| apt repo
salt_apt_repo_key| "http://apt.mccartney.ie/KEY"| apt repo key 
chef_bootstrap_url| "https://www.getchef.com/chef/install.sh"| the chef bootstrap installer, used to provide Ruby for the serverspec test runner on the guest OS
salt_config| "/etc/salt"|
salt_minion_config| "/etc/salt/minion"|
salt_file_root| "/srv/salt"|
salt_pillar_root| "/srv/pillar"|
salt_state_top| "/srv/salt/top.sls"|
salt_run_highstate| true |
state_top| {} | states that should be applied, in standard top.sls format
pillars| {} | pillar data 
pillars-from-files | | a list of key-value pairs for files that should be loaded as pillar data


##Configuring Provisioner Options
The provisioner can be configured globally or per suite, global settings act as defaults for all suites, you can then customise per suite, for example:
    
    ---
    driver:
      name: vagrant
    
    provisioner:
      name: salt_solo
      formula: beaver
      pillars:
      state_top:
        base:
          '*':
            - beaver
            - beaver.ppa
    
    platforms:
      - name: ubuntu-12.04
    
    suites:
      - name: default
    
      - name: default_0162
        provisioner:
          salt_version: 0.16.2
          salt_install: apt
          
in this example, the default suite will install salt via the bootstrap method, meaning that it will get the latest package available for the platform via the [bootstrap shell script](http://bootstrap.saltstack.org). We then define another suite called `default_0162`, this has the provisioner install salt-0.16.2 via apt-get (this defaults to a mini repo of mine, which you can override, my repo only contains 0.16.2)

### [pillars-from-files](id:pillars-from-files)
The pillars-from-files option allows you to load pillar data from an external file, instead of being embedded in the .kitchen.yml.  This allows you to re-use the example files or reduce the clutter in your .kitchen.yml

Consider the following suite definition:
    
      - name: tcp-output-external-pillar
        provisioner:
          pillars-from-files:
            beaver.sls: beaver-example.sls
          pillars:
            top.sls:
              base:
                '*':
                  - beaver

In this example, the beaver pillar is loaded from the example file in the repo, ``beaver-example.sls``, but we can still define the ``top.sls`` inline in the .kitchen.yml file. 