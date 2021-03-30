VAGRANTFILE_API_VERSION = '2'

ANSIBLE_VERSION = "2.3.0.0"

ANSIBLE_ROLE = 'ansible-role-nginx'

EPEL_REPO_7 = '''
[epel]
name     = EPEL 7 - \$basearch
baseurl  = http://mirror.globo.com/epel/7/\$basearch
enabled  = 1
gpgcheck = 0
'''

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.provider :virtualbox do |vb|
    vb.gui = false
    vb.customize [ 'modifyvm', :id, '--memory', '512' ]
    vb.customize [ 'modifyvm', :id, '--nictype1', 'virtio' ]
    vb.customize [ 'modifyvm', :id, '--natdnshostresolver1', 'on' ]
    vb.customize [ 'modifyvm', :id, '--natdnsproxy1', 'on' ]
  end

  config.vm.define 'ubuntu-focal' do |ubuntu_f|
    ubuntu_f.vm.box      = 'ubuntu/focal64'
    ubuntu_f.vm.hostname = 'ubuntu-focal'
    
    ubuntu_f.vm.provision 'shell', inline: 'apt-get update'
    ubuntu_f.vm.provision 'shell', inline: 'apt-get install -y -qq  python2 libffi-dev libssl-dev python2-dev gcc'
    ubuntu_f.vm.provision 'shell', inline: 'curl -s https://bootstrap.pypa.io/pip/2.7/get-pip.py --output - | python2 -'
    ubuntu_f.vm.provision 'shell', inline: "pip install -q ansible==#{ANSIBLE_VERSION} ansible-lint jinja2"
    ubuntu_f.vm.provision 'shell', inline: "ln -sf /vagrant /vagrant/#{ANSIBLE_ROLE}"

    ubuntu_f.vm.provision 'ansible_local' do |ansible|
      ansible.playbook = 'tests/test_vagrant.yml'
      ansible.extra_vars = {
        :ansible_python_interpreter => "/usr/bin/python2"
      }
    end
    
  end

  config.vm.define 'centos-7' do |centos7|
    centos7.vm.box      = 'centos/7'
    centos7.vm.hostname = 'centos-7'

    centos7.vm.provision 'shell', inline: 'yum install -y ca-certificates'
    centos7.vm.provision 'shell', inline: "echo \"#{EPEL_REPO_7}\" > /etc/yum.repos.d/epel.repo"
    centos7.vm.provision 'shell', inline: 'yum install -y python-devel gcc libffi-devel openssl-devel'
    centos7.vm.provision 'shell', inline: 'setenforce 0'
    centos7.vm.provision 'shell', inline: "curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o - | python"
    centos7.vm.provision 'shell', inline: "pip install -q ansible==#{ANSIBLE_VERSION} ansible-lint jinja2"
    centos7.vm.provision 'shell', inline: "ln -sf /vagrant /vagrant/#{ANSIBLE_ROLE}"

    centos7.vm.provision 'ansible_local' do |ansible| 
      ansible.playbook = 'tests/test_vagrant.yml'
      ansible.extra_vars = {
      }
    end
  end

end
  
# vi:ts=2:sw=2:et:ft=ruby:
