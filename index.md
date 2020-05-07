## AnsibleでFortiGateを動かしてみよう

---

<h2 id="pro"><font color="#9164CC">序. はじめに</font></h2>
 <h4>LAB環境</h4>
  ubuntu server 18.04<br>
  FortiGate-VM64-KVM v6.2.3<br>
  pip3 (python3)<br>
  Ansible 2.9.6<br>
  fortiosapi 1.0.1<br>
  <br>
 <h4>用意するもの</h4>
  ubuntu server 18.04<br>
  fortios 6.2.3 kvm qcow2<br>
  fortigate vmlicense<br>
  <br>

<h2 id="lab0"><font color="#9164CC">LAB環境セットアップ</font></h2>
 <h4>1. ubuntuにsshアクセス</h4>
 <h4>2. 演習用ドキュメント一式をダウンロード</h4>
  $ git clone https://github.com/fortinet-solutions-cse/testbeds.git<br>
  $ git clone https://github.com/fortinet-solutions-cse/40ansible.git<br>
 <h4>3. 各種パッケージのインストール</h4>
  $ cd testbeds/<br>
  $ ./installation.sh<br>
  <p style="color:#0071CE">Generating public/private rsa key pairで対話式でいくつか聞かれるが、全部EnterでOk.</p>
 <h4>4. ubuntuをリブート</h4>
  $ sudo reboot<br>
 <h4>5. pip3 インストール</h4>
  $ sudo apt install -y python3-pip<br>
 <h4>6. Ansible 2.9.6インストール</h4>
  $ pip3 install ansible==2.9.6<br>
 <h4>7. PATHを通す</h4>
  $ echo "export PATH=$PATH:/home/${USER}/.local/bin" >>.bashrc<br>
  $ source ~/.bashrc<br>
  <h4>8. Ansibleバージョン確認</h4>
  $ ansible-playbook --version
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook --version
ansible-playbook 2.9.6
  config file = None
  configured module search path = ['/home/hu/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/hu/.local/lib/python3.6/site-packages/ansible
  executable location = /home/hu/.local/bin/ansible-playbook
  python version = 3.6.9 (default, Apr 18 2020, 01:56:04) [GCC 8.4.0]</pre>
  <h4>9. fortiosapiインストール</h4>
  $ pip3 install fortiosapi==1.0.1<br>
  <h4>10. fortiosapiのバージョン確認</h4>
  $ pip3 freeze | grep fortiosapi
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
  $ pip3 freeze | grep fortiosapi
  fortiosapi==1.0.1</pre>
  <h4>11. kvm(fortios_623.qcow2)をホームディレクトリにアップロード</h4>
  <h4>12. fortigate vm起動</h4>
  cd testbeds/fortigate/<br>
  ./start_fgt621.sh ../../fortios_621.qcow2<br>
  <h4>13. vm起動確認</h4>
  $ virsh list
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">  
  $ virsh list
 Id    Name                           State
----------------------------------------------------
 1     fortigate                      running</pre>
 <h4>14. kvmコンソールでログイン</h4>
  $ virsh console fortigate<br> 
  <p style="color:#0071CE"> Username: admin, Password: なし</p>
  <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">  
 $ virsh console fortigate
Connected to domain fortigate
Escape character is ^]
FortiGate-VM64-KVM login: admin
Password: </pre> 
 <h4>15. New Passwordで`admin`を設定H/h4>
  <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">  
  You are forced to change your password, please input a new password.
New Password:*****
Confirm Password:*****
Welcome !  </pre>
 <h4>16. cloudinitで流しんだコンフィグ情報を確認</h4>
  $ diagnoze debug cloudinit show
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">   
 # diagnose debug cloudinit show
 >> FortiGate-VM64-KVM $  config system interface
 >> FortiGate-VM64-KVM (interface) $    edit "port1"
 >> FortiGate-VM64-KVM (port1) $      set vdom "root"
 >> FortiGate-VM64-KVM (port1) $      set mode static
 >> FortiGate-VM64-KVM (port1) $      set ip 192.168.122.40/24
 >> FortiGate-VM64-KVM (port1) $      set allowaccess https ping ssh snmp http telnet fgfm radius-acct probe-response capwap ftm
 >> FortiGate-VM64-KVM (port1) $    next
 >> FortiGate-VM64-KVM (interface) $  end
 >> FortiGate-VM64-KVM $  config system dns
 >> FortiGate-VM64-KVM (dns) $    set primary 8.8.8.8
 >> FortiGate-VM64-KVM (dns) $    set secondary 8.8.4.4
 >> FortiGate-VM64-KVM (dns) $  end
 >> FortiGate-VM64-KVM $  config router static
 >> FortiGate-VM64-KVM (static) $    edit 2
 >> FortiGate-VM64-KVM (2) $      set gateway 192.168.122.1
 >> FortiGate-VM64-KVM (2) $      set device "port1"
 >> FortiGate-VM64-KVM (2) $    next
 >> FortiGate-VM64-KVM (static) $  end
 >> FortiGate-VM64-KVM $  config system global
 >> FortiGate-VM64-KVM (global) $    set admintimeout 480
 >> FortiGate-VM64-KVM (global) $  end</pre>
 <h4>17. Control+]でconsoleから抜ける</h4>
  
<h2 id="lab1"><font color="#9164CC">LAB1: ファイアウォールポリシーの設定</font></h2>
 <h4>1. 40ansibleディレクトリに入る</h4>
  $ cd ~/40ansible/<br>
 <h4>2. examplesディレクトリにあるfortigate_create_firewall_policy.ymlプレイブックをカレントディレクトリにコピー</h4>
  $ cp examples/fortigate_create_firewall_policy.yml .<br>
 <h4>3. fortigate_create_firewall_policy.ymlプレイブックを確認</h4>
  $ more fortigate_create_firewall_policy.yml<br>
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
 $ more fortigate_create_firewall_policy.yml
- hosts: localhost
# strategy: debug
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "root"
  tasks:
  - name: create firewall policy
    fortiosconfig:
     config: "firewall policy"
     action: "set"
     host: "{{ host }}"
     username: "{{ username }}"
     password: "{{ password }}"
     vdom: "{{ vdom }}"
     https: False
     ssl_verify: False
--省略--
</pre>
  <p style="color:#0071CE">NOTE1: このプレイブックでは、fortiosconfigモジュールを使うことがわかります。Ansibleでは、モジュールを探索するためにプレイブックの置かれたディレクトリ内のlibraryディレクトリを参照します。2. でプレイブックをコピーしたのはこのためです。</p>
  <p style="color:#0071CE">NOTE2: この演習では、まずお試しライセンスで行います。お試しライセンスでは、管理アクセスがhttpのみに限られますので、https: Falseとしています。</p>
 <h4>4. libraryディレクトリにfortiosconfigモジュールがあることを確認</h4>
  $ ls library<br>
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ls library
forticare_download_license.py   forticare_get_assets.py        forticare_register_units.py  fortimailconfig.py
forticare_get_asset_details.py  forticare_register_license.py  fortimail.py                 fortiosconfig.py
</pre>
 <h4>5. プレイブック実行前の、設定確認</h4>
  $ ansible-playbook fortigate_create_firewall_policy.yml<br>
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ssh admin@192.168.122.40 show firewall policy
admin@192.168.122.40's password:
FortiGate-VM64-KVM # config firewall policy
end
</pre>
 <h4>6. プレイブック実行</h4>
 $ ansible-playbook fortigate_create_firewall_policy.yml<br>
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
 $ ansible-playbook fortigate_create_firewall_policy.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [localhost] ***************************************************************************************

TASK [Gathering Facts] *********************************************************************************
ok: [localhost]

TASK [create firewall policy] **************************************************************************
changed: [localhost]

PLAY RECAP *********************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
</pre>
 <h4>7. プレイブック実行後の設定確認</h4>
  $ ssh admin@192.168.122.40 show firewall policy<br>
 <pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook fortigate_create_firewall_policy.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [localhost] ***************************************************************************************

TASK [Gathering Facts] *********************************************************************************
ok: [localhost]

TASK [create firewall policy] **************************************************************************
changed: [localhost]

PLAY RECAP *********************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
</pre>

<h2 id="lab2"><font color="#9164CC">LAB2: スタティックルートの設定</font></h2>
#### 1.
  **********<br>
  **********<br>
#### 2.
  **********<br>
  **********<br>
#### 3.
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  
<h2 id="lab3"><font color="#9164CC">LAB3: ライセンスのアップロード</font></h2>
#### 1.
<br>**********<br>
<br>**********<br>
#### 2.
<br>**********<br>
<br>**********<br>
#### 3.
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>
<br>**********<br>

<h2 id="lab4"><font color="#9164CC">LAB4: スタティックURLフィルタの設定<font></h2>
#### 1.
  **********<br>
  **********<br>
#### 2.
  **********<br>
  **********<br>
#### 3.
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  
<h2 id="lab5"><<font color="#9164CC">LAB5: Rolesの活用</font></h2>
#### 1.
  **********<br>
  **********<br>
#### 2.
  **********<br>
  **********<br>
#### 3.
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>

<h2 name="lab6"><font color="#9164CC">LAB6: inventoryとhttpapiの活用</font></h2>
#### 1.
  **********<br>
  **********<br>
#### 2.
  **********<br>
  **********<br>
#### 3.
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  
<h2 name="lab7"><font color="#9164CC">LAB7: ファイアウォールポリシーのルックアップ</font></h2>
#### 1.
  **********<br>
  **********<br>
#### 2.
  **********<br>
  **********<br>
#### 3.
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
  **********<br>
---

### Category Name 2

- [Project 1 Title](http://example.com/)
- [Project 2 Title](http://example.com/)
- [Project 3 Title](http://example.com/)
- [Project 4 Title](http://example.com/)
- [Project 5 Title](http://example.com/)

---




---
<p style="font-size:11px">Page template forked from <a href="https://github.com/evanca/quick-portfolio">evanca</a></p>
<!-- Remove above link if you don't want to attibute -->
