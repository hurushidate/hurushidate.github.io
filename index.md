<br><br>
<a name="pro"/>

---
### はじめに
---
<h4>LAB環境</h4>
 ubuntu server 18.04<br>
 FortiGate-VM64-KVM v6.2.3<br>
 pip3 (python3.6.9)<br>
 Ansible 2.9.6<br>
 fortiosapi 1.0.1<br>

<h4>用意するもの</h4>
 ubuntu server 18.04<br>
 fortios 6.2.3 kvm qcow2<br>
 fortigate VMライセンス<br>

<br><br>
<a name="lab0"/>

---
### 環境セットアップ
---
<h4>1. ubuntuにsshアクセス</h4>

<h4>2. 演習用ドキュメント一式をダウンロード</h4>
	git clone https://github.com/fortinet-solutions-cse/testbeds.git
	git clone https://github.com/fortinet-solutions-cse/40ansible.git

<h4>3. 各種パッケージのインストール</h4>
	cd testbeds/
	./installation.sh
<p style="color:#9164CC">SSHの鍵生成ダイアローグがでてきます。すべてEnterで大丈夫です。</p>

<h4>4. ubuntuをリブート</h4>
	sudo reboot

<h4>5. pip3 インストール</h4>
	sudo apt install -y python3-pip

<h4>6. Ansible 2.9.6インストール</h4>
	pip3 install ansible==2.9.6

<h4>7. PATHを通す</h4>
	echo "export PATH=$PATH:/home/${USER}/.local/bin" >>.bashrc
	source ~/.bashrc

<h4>8. Ansibleバージョン確認</h4>
	ansible-playbook --version
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook --version
ansible-playbook 2.9.6
  config file = None
  configured module search path = ['/home/hu/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/hu/.local/lib/python3.6/site-packages/ansible
  executable location = /home/hu/.local/bin/ansible-playbook
  python version = 3.6.9 (default, Apr 18 2020, 01:56:04) [GCC 8.4.0]</pre>
  
<h4>9. fortiosapiインストール</h4>
	pip3 install fortiosapi==1.0.1

<h4>10. fortiosapiのバージョン確認</h4>
	pip3 freeze | grep fortiosapi
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ pip3 freeze | grep fortiosapi
fortiosapi==1.0.1</pre>

<h4>11. kvm(fortios_623.qcow2)をホームディレクトリにアップロード</h4>

<h4>12. fortigate vm起動</h4>
	cd testbeds/fortigate/
	./start_fgt.sh ../../fortios_623.qcow2

<h4>13. vm起動確認</h4>
	virsh list
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ virsh list
 Id    Name                           State
----------------------------------------------------
 1     fortigate                      running</pre>

<h4>14. kvmコンソールでログイン</h4>
	virsh console fortigate
<p style="color:#9164CC"> Username: admin, Password: なし</p>
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ virsh console fortigate
Connected to domain fortigate
Escape character is ^]
FortiGate-VM64-KVM login: admin
Password: </pre> 

<h4>15. パスワードの変更を促すプロンプトがでるので<code>admin</code>を設定</h4>
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
You are forced to change your password, please input a new password.
New Password:*****
Confirm Password:*****
Welcome !  </pre>

<h4>16. cloudinitで流しんだコンフィグ情報を確認</h4>
	diagnoze debug cloudinit show
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

<br><br>
<a name="lab1"/>

---
### LAB1: ファイアウォールポリシーの設定
---
<h4>1. 40ansibleディレクトリに入る</h4>
	cd ~/40ansible/

<h4>2. examplesディレクトリにあるfortigate_create_firewall_policy.ymlプレイブックをカレントディレクトリにコピー</h4>
	cp examples/fortigate_create_firewall_policy.yml .

<h4>3. fortigate_create_firewall_policy.ymlプレイブックを確認</h4>
	more fortigate_create_firewall_policy.yml
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
...省略...</pre>

<p style="color:#9164CC">解説1: このプレイブックでは、fortiosconfigモジュールを使うことがわかります。Ansibleでは、モジュールを探索するためにプレイブックの置かれたディレクトリ内のlibraryディレクトリを参照します。2. でプレイブックをコピーしたのはこのためです。</p>
<p style="color:#9164CC">解説2: この演習では、まずお試しライセンスで行います。お試しライセンスでは、管理アクセスがhttpのみに限られますので、https: Falseとしています。</p>

<h4>4. libraryディレクトリにfortiosconfigモジュールがあることを確認</h4>
	ls library
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ls library
forticare_download_license.py   forticare_get_assets.py        forticare_register_units.py  fortimailconfig.py
forticare_get_asset_details.py  forticare_register_license.py  fortimail.py                 fortiosconfig.py</pre>

<h4>5. プレイブック実行前の、設定確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 show firewall policy
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 show firewall policy
FortiGate-VM64-KVM # config firewall policy
end</pre>

<h4>6. プレイブック実行</h4>
	ansible-playbook fortigate_create_firewall_policy.yml
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
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0</pre>

<h4>7. プレイブック実行後の設定確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 show firewall policy
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 show firewall policy
FGT-VM64-KVM # config firewall policy
    edit 1
        set name "ansible"
        set uuid 5c957b3c-910b-51ea-140e-d7e6496a3cba
        set srcintf "any"
        set dstintf "any"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "HTTPS"
        set logtraffic all
    next
end
</pre>

<br><br>
<a name="lab2"/>

---
### LAB2: スタティックルートの設定
---
<h4>1. library内のfortiosconfigモジュールでCONFIG_CALLSを検索</h4>
	more library/fortiosconfig.py
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
~/40ansible$ more library/fortiosconfig.py
...省略...
CONFIG_CALLS = [
    'alertemail setting',
    'antivirus heuristic',
    'antivirus profile',
    'antivirus quarantine',
…
    'router static',
    'router static6',
    'spamfilter bwl',
...省略...
</pre>
<p style="color:#9164CC">CONFIG_CALLSにある設定項目がfortiosconfigモジュールで設定可能です。</p>

<h4>2. REST APIスキーマの確認</h4>
<h5>2.1	APIログインのためのクッキー情報取得</h5>
	curl http://192.168.122.40/logincheck -H 'Cache-Control: cache' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=admin&secretkey=admin&ajax=1' -c cookies
<h5>2.2	スキーマの取得</h5>
	curl -b cookies -X GET 'http://192.168.122.40/api/v2/cmdb/router/static?action=schema'
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ curl -b cookies -X GET 'http://192.168.122.40/api/v2/cmdb/router/static?action=schema'
{
  "http_method":"GET",
  "revision":"20b4c7ed3916791a3efd6e596513f42c",
  "results":{
    "name":"static",
    "category":"table",
    "help":"Configure IPv4 static routing tables.",
    "mkey":"seq-num",
    "mkey_type":"integer",
    "children":{
      "seq-num":{
        ...
      },
      "status":{
      },
      "dst":{
      },
      "src":{
      },
...省略...
</pre>
<p style="color:#9164CC">master-key(mkye)がseq-numで、children配下に設定アトリビュートとして、seq-num, status, dst, src, などがあることがわかります。プレイブックを作成するときに、これらのアトリビュートを使用します。</p>

<h4>3. Lab1で使ったプレイブックをコピーし、スタティックルート設定用のプレイブック(fortigate_modify_router_static.yml)を作成</h4>
	cp fortigate_create_firewall_policy.yml fortigate_modify_router_static.yml

<h4>4. プレイブックを編集</h4>
	sudo vi fortigate_modify_router_static.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
- hosts: localhost
#  strategy: debug
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "root"
  tasks:
  - name: create firewall policy
    fortiosconfig:
     config: "router static"
     action: "set"
     host: "{{ host }}"
     username: "{{ username }}"
     password: "{{ password }}"
     vdom: "{{ vdom }}"
     https: False
     ssl_verify: False
     config_parameters:
       seq-num: "5"
       dst: "192.168.130.0/23"
       gateway: "192.168.130.1"
       device: "port1"
</pre>

<h4>5. プレイブック実行前の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 show router static
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 show router static
FortiGate-VM64-KVM # config router static
    edit 2
        set gateway 192.168.122.1
        set device "port1"
    next
end
</pre>

<h4>6. プレイブック実行</h4>
	ansible-playbook fortigate_modify_router_static.yml

<h4>7. プレイブック実行後の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 show router static
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 show router static
FortiGate-VM64-KVM # config router static
    edit 2
        set gateway 192.168.122.1
        set device "port1"
    next
    edit 5
        set dst 192.168.130.0 255.255.254.0
        set gateway 192.168.130.1
        set device "port1"
    next
end
</pre>

<br><br>
<a name="lab3"/>

---
### LAB3: ライセンスのアップロード
---
<h4>1. ライセンスアップロード用のプレイブックを確認</h4>
	cd ~/40ansible/examples
	more fortigate_upload_license.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ more fortigate_upload_license.yml
- hosts: localhost
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "global"
  tasks:
  - name: Upload license
    fortiosconfig:
     config: "system vmlicense upload"
     action: "upload"
     host:  "{{ host }}"
     username: "{{ username }}"
     password: "{{ password }}"
     vdom:  "{{ vdom }}"
     https: False
     ssl_verify: False
     config_parameters:
      filename: "license_file.lic"
</pre>
	
<h4>2. プレイブック実行前の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 "get sys status | grep Serial"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "get sys status | grep Serial"
FortiGate-VM64-KVM # Serial-Number: FGVMEV******
</pre>
<p style="color:#9164CC">FGVMEV******はお試し用ライセンス(EValuation)です。</p>

<h4>3. プレイブック実行 - 1st</h4>
	ansible-playbook fortigate_upload_license.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook fortigate_upload_license.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'
ERROR! couldn't resolve module/action 'fortiosconfig'. This often indicates a misspelling, missing collection, or incorrect module path.

The error appears to be in '/home/hu/40ansible/examples/fortigate_upload_license.yml': line 8, column 5, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

  tasks:
  - name: Upload license
    ^ here
</pre>
<p style="color:#9164CC">fortiosconfigモジュールが見つからないと言われています。LAB1で説明したとおり、Ansibleではモジュール探索のため、プレイブックの置かれたディレクトリのlibraryディレクトリを探します。ここではlibraryディレクトリがないため、エラーが出ています。</p>

<h4>4. 環境変数ANSIBLE_LIBRARYに、fortiosconfigモジュールのあるlibraryディレクトリを指定</h4>
	declare -x ANSIBLE_LIBRARY=~/40ansible/library

<h4>5. プレイブック実行 - 2nd</h4>
	ansible-playbook fortigate_upload_license.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook fortigate_upload_license.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************
ok: [localhost]

TASK [Upload license] *******************************************************************************************************

An exception occurred during task execution. To see the full traceback, use -vvv. The error was: FileNotFoundError: [Errno 2] No such file or directory: 'license_file.lic'
...省略...
</pre>

<p style="color:#9164CC">ライセンスファイルが見つからないと言われています。</p>
	
<h4>6. ライセンスアップロード</h4>
~/40ansible/examplesに<code> license_file.lic </code>というファイル名でライセンスファイルをアップロードします。

	scp <ローカルのライセンスファイルpath> <username>@<ubuntu_server_ip>:~/40ansible/examples/license_file.lic

<h4>7. プレイブック実行 - 3rd</h4>
	ansible-playbook fortigate_upload_license.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook fortigate_upload_license.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************
ok: [localhost]

TASK [Upload license] *******************************************************************************************************
changed: [localhost]

PLAY RECAP *******************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
</pre>

<h4>8. プレイブック実行後の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 "get sys status | grep Serial"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "get sys status | grep Serial"
FGVM04TM20000646 # Serial-Number: FGVM04*********
</pre>

<h4>9. プレイブック実行 - 4th</h4>
	ansible-playbook fortigate_upload_license.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
ansible-playbook fortigate_upload_license.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************
ok: [localhost]

TASK [Upload license] *******************************************************************************************************
fatal: [localhost]: FAILED! => {"changed": false, "module_stderr": "/home/hu/.local/lib/python3.6/site-packages/urllib3/connectionpool.py:986: InsecureRequestWarning: Unverified HTTPS request is being made to host '192.168.122.40'. Adding certificate verification is strongly advised.
...省略...
</pre>
<p style="color:#9164CC">ライセンスが適用されると、管理アクセスがHTTPSにリダイレクトされます。AnsibleではHTTPでリクエストを投げているため不一致でエラーがでています。</p>

<h4>10. これまでのプレイブックをベースに、HTTPS用のプレイブックを作成</h4>
	cp fortigate_upload_license.yml fortigate_upload_license_https.yml
	sudo vi fortigate_upload_license_https.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sudo vi fortigate_upload_license.yml
- hosts: localhost
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "global"
  tasks:
  - name: Upload license
    fortiosconfig:
     config: "system vmlicense upload"
     action: "upload"
     host:  "{{ host }}"
     username: "{{ username }}"
     password: "{{ password }}"
     vdom:  "{{ vdom }}"
     https: True
     ssl_verify: False
     config_parameters:
      filename: "license_file.lic"
</pre>
<p style="color:#9164CC">https: True にします</p>

<h4>11. プレイブック実行 - 5th</h4>
	ansible-playbook fortigate_upload_license_https.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook fortigate_upload_license_https.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************
ok: [localhost]

TASK [Upload license] *******************************************************************************************************
changed: [localhost]

PLAY RECAP *******************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
</pre>

<br><br>
<a name="lab4"/>

---
### LAB4: スタティックURLフィルタの設定
---
LAB1からLAB3で使用したfortiosconfigモジュールは、Ansible公式のモジュールではありません。
このLabでは、Ansibleコアにあるモジュールを使って設定します。
fortiosconfigモジュールとは異なり、Ansible公式モジュールは設定項目ごとにわかれています。
また、パラメータ説明や使用例などのドキュメントも整備されていて、FortiGate REST APIのスキーマを確認しなくてもプレイブックを書けるようになっています。

<h4>1. Ansible公式モジュールの確認</h4>
<h5>1-1	Ansible公式モジュールの格納場所を確認</h5>
	ansible-playbook --version
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ansible-playbook --version
ansible-playbook 2.9.6
  config file = None
  configured module search path = ['/home/testuser/40ansible/library']
  ansible python module location = /home/testuser/.local/lib/python3.6/site-packages/ansible
  executable location = /home/testuser/.local/bin/ansible-playbook
  python version = 3.6.9 (default, Apr 18 2020, 01:56:04) [GCC 8.4.0]
</pre>
  
<h5>1-2	fortios用のモジュールを確認</h5>
	ls /home/testuser/.local/lib/python3.6/site-packages/ansible/modules/network/fortios

今回は、この中のfortios_webfilter_urlfilter.pyを使います。

<h5>1-3	fortios_webfilter_urlfilter.pyのDOCUMENTATION部分を確認</h5>
	more /home/testuser/.local/lib/python3.6/site-packages/ansible/modules/network/fortios/fortios_webfilter_urlfilter.py
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ more /home/testuser/.local/lib/python3.6/site-packages/ansible/modules/network/fortios/fortios_webfilter_urlfilter.py
...省略...
DOCUMENTATION = '''
---
module: fortios_webfilter_urlfilter
short_description: Configure URL filter lists in Fortinet's FortiOS and FortiGate.
description:
    - This module is able to configure a FortiGate or FortiOS (FOS) device by allowing the user to set and modify webfilter feature and urlfilter category. Examples include all parameters and values need to be adjusted to datasources before usage. Tested with FOS v6.0.5
version_added: "2.8"
notes:
    - Requires fortiosapi library developed by Fortinet
    - Run as a local_action in your playbook
requirements:
    - fortiosapi>=0.9.8
options:
    host:
        description:
            - FortiOS or FortiGate IP address.
        type: str
        required: false
...省略...
'''
</pre>

<h5>1-4	Ansible公式ページで確認</h5>
モジュールのソースコードは<a href="https://docs.ansible.com/ansible/latest/modules/fortios_webfilter_urlfilter_module.html#fortios-webfilter-urlfilter-module">公式ページ</a>でも確認可能です。

<h4>2. プレイブックの内容を確認</h4>
	cd ~/40ansible/examples
	more fortigate_webfilter_add_url.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ more fortigate_webfilter_add_url.yml
...省略...
  tasks:
  - name: Configure url to be filtered by fortigate
    fortios_webfilter_urlfilter:
      host:  "{{  host }}"
      username: "{{  username}}"
      password: "{{ password }}"
      vdom:  "{{  vdom }}"
      https: False
      ssl_verify: False
      webfilter_urlfilter:
        state: "present"
        id: "1"
        name: "default"
        one_arm_ips_urlfilter: "disable"
        ip_addr_block: "disable"
        entries:
          - id: "1"
            url: "www.test1.com"
            type: "simple"
...省略...
</pre>

<h4>3. プレイブック実行前の設定確認</h4>
	sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
FortiGate-VM64-KVM # config webfilter urlfilter
end
</pre>

<h4>4. プレイブック実行</h4>
	ansible-playbook fortigate_webfilter_add_url.yml

<h4>5. プレイブック実行後の設定確認</h4>
	sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
FortiGate-VM64-KVM # config webfilter urlfilter
    edit 1
        set name "default"
        set comment "mycomment"
        config entries
            edit 1
                set url "www.test1.com"
                set exempt pass
            next
            edit 2
                set url "www.test2.com"
                set exempt pass
            next
        end
    next
end
</pre>

<h4>6. これまでのプレイブックをベースに、設定を削除するためのプレイブックを作成</h4>
	cp fortigate_webfilter_add_url.yml fortigate_webfilter_remove_url.yml
	sudo vi fortigate_webfilter_remove_url.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$sudo vi fortigate_webfilter_remove_url.yml
- hosts: localhost
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "root"
  tasks:
  - name: Configure url to be filtered by fortigate
    fortios_webfilter_urlfilter:
      host:  "{{  host }}"
      username: "{{ username }}"
      password: "{{ password }}"
      vdom:  "{{  vdom }}"
      https: False
      ssl_verify: False
      webfilter_urlfilter:
        state: "absent"
        id: "1"
</pre>
<p style="color:#9164CC">state: "absent"に変更し、id依り下のアトリビュート削除</p>

<h4>7. プレイブック実行</h4>
	ansible-playbook fortigate_webfilter_remove_url.yml

<h4>8. プレイブック実行後の設定確認</h4>
	sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40  show webfilter urlfilter
FortiGate-VM64-KVM # config webfilter urlfilter
end
</pre>
  

<br><br>
<a name="lab5"/>

---
### <a name="lab5">LAB5: Rolesの活用</a>
---
Rolesは特定のvars_fileやtasks、handlersをあらかじめ決められたファイル構造で置くと、自動的にロードされる仕組みです。
複雑で膨大な処理を書きたい場合、1つのプレイブックに全て書くのではなく、Rolesを使います。
	
<h4>1. fortiroleという名前でロールを作成</h4>
	cd ~/40ansible
	ansible-galaxy init fortirole

<h4>2. 自動生成されたfortiroleのディレクトリ構造を確認</h4>
	ll ~/40ansible/fortirole
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ ll fortirole
total 48
drwxrwxr-x 10 hu hu 4096 May  1 03:47 ./
drwxrwxr-x  7 hu hu 4096 May  1 04:44 ../
-rw-rw-r--  1 hu hu  539 May  1 03:47 .travis.yml
-rw-rw-r--  1 hu hu 1328 May  1 03:47 README.md
drwxrwxr-x  2 hu hu 4096 May  1 03:47 defaults/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 files/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 handlers/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 meta/
drwxrwxr-x  2 hu hu 4096 May  1 04:45 tasks/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 templates/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 tests/
drwxrwxr-x  2 hu hu 4096 May  1 03:47 vars/
</pre>

<h4>3. 新規にプレイブックを作成し、rolesアトリビュートを追加</h4>
	sudo vi fortirole.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sudo vi fortirole.yml
- hosts: localhost
  roles:
  - { role: "fortirole" }
 </pre>

<h4>4. 予め用意されている2つのプレイブックをfortirole/tasksにコピー</h4>
	cp fortigate_create_firewall_address.yml ~/40ansible/fortirole/tasks/

<h4>5. コピー先の2つのプレイブックのtasksアトリビュートのみ残す</h4>
	cd ~/40ansible/fortirole/tasks
	sudo vi fortigate_create_firewall_address.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
  - name: Configure IPv4 addresses.
    fortios_firewall_address:
      host:  "{{ host }}"
      username: "{{ username }}"
      password: "{{ password }}"
      vdom:  "{{ vdom }}"
      https: True
      ssl_verify: False
      state: "present"
      firewall_address:
        name: "AnsibleTestFirewallAddress"
        subnet: "200.200.200.0/24"
        type: "ipmask"
</pre>
	sudo vi fortigate_create_system_api_user.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
  - name: Configure API users.
    fortios_system_api_user:
      host:  "{{ host }}"
      username: "{{ username }}"
      password: "{{ password }}"
      vdom:  "{{ vdom }}"
      https: "True"
      ssl_verify: "False"
      state: "present"
      system_api_user:
        accprofile: "super_admin"
        name: "AnsibleTestApiUser"
        trusthost:
         -
            id:  "1"
            ipv4-trusthost: "192.168.0.0 255.255.0.0"
</pre>

<h4>6. tasksディレクトリにあるmain.ymlを編集し、2つのtasksを入れる(import_tasks)</h4>
	cd ~/40ansible/fortirole/tasks
	sudo vi main.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
# tasks file for fortirole
- import_tasks: fortigate_create_firewall_address.yml
- import_tasks: fortigate_create_system_api_user.yml
</pre>

<h4>7. varsディレクトリにあるmain.ymlを編集し、もとのプレイブックのvarsアトレリビュートをコピペ</h4>
	cd ~/40ansible/fortirole/vars
	sudo vi main.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
# vars file for fortirole
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "root"
</pre>

<h4>8. プレイブック実行前の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 "show firewall address | grep -f Ansible"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "show firewall address | grep -f Ansible"
FGVM04TM20000646 #
</pre>
	sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
FGVM04TM20000646 #
</pre>


<h4>9. プレイブック実行</h4>
	ansible-playbook fortirole.yml

<h4>10. プレイブック実行後の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 "show firewall address | grep -f Ansible"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "show firewall address | grep -f Ansible"
FGVM04TM20000646 # config firewall address
    edit "AnsibleTestFirewallAddress" <---
        set uuid 7b5be672-8b76-51ea-edfc-58c4bfc8cc6d
        set subnet 200.200.200.0 255.255.255.0
    next
end"
</pre>
	sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
FGVM04TM20000646 # config system api-user
    edit "AnsibleTestApiUser" <---
        set accprofile "super_admin"
        set vdom "root"
    next
end
</pre>



<br><br>
<a name="lab6"/>

---
### <a name="lab6">LAB6: inventoryとhttpapiプラグインの活用</a>
---
Inventoryファイル(hostsファイル)を使うことで、複数のFortiGateに対して同じコマンドを実施できます。
これまでのLABでは、Ansible構文をFortiOS REST APIにコンバートする際に、fortiosapiライブラリを使ってきました。Ansible 2.9以降に追加されたモジュールでは、httpapiプラグインも使用できるようになっています。

<h4>1. カレントディレクトリにhostsという名前でInventoryファイルを作成</h4>
	sudo vi hosts
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sudo vi hosts
all:
  children:
    fortios:
      hosts:
        fortios01:
          ansible_host: 192.168.122.40
      vars:
        ansible_user: admin
        ansible_password: admin
        ansible_network_os: fortios
        ansible_connection: httpapi
        ansible_httpapi_use_ssl: yes
        ansible_httpapi_validate_certs: no
        ansible_python_interpreter: /usr/bin/python3
        vdom: root
</pre>

<h4>2. カレントディクトリのfortigate_create_system_api_user.ymlを編集</h4>
	sudo vi fortigate_create_system_api_user.yml
	
LAB5の最後に作成したAPIユーザを削除するために、<code>state: absent</code>とします。
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sudo vi fortigate_create_system_api_user.yml
- hosts: fortios
  tasks:
  - name: Configure API users.
    fortios_system_api_user:
      host:  "{{ ansible_host }}"
      username: "{{ ansible_user }}"
      password: "{{ ansible_password }}"
      vdom:  "{{ vdom }}"
      https: "True"
      ssl_verify: "False"
      state: "absent"
      system_api_user:
        accprofile: "super_admin"
        name: "AnsibleTestApiUser"
        trusthost:
         -
            id:  "1"
            ipv4-trusthost: "192.168.0.0 255.255.0.0"
            type: "ipv4-trusthost"
</pre>

<h4>3. プレイブック実行</h4>
	ansible-playbook -i hosts fortigate_create_system_api_user.yml

<h4>4. プレイブック実行後の確認</h4>
	sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sshpass -p admin ssh admin@192.168.122.40 "show sys api-user | grep -f Ansible"
FGVM04TM20000646 #
</pre>
  
<br><br>
<a name="lab7"/>

---
### <a name="lab7">LAB7: ファイアウォールポリシーのルックアップ</a>
---
FortiOSはREST APIは、CMDB APIとMonitor APIの2種類あります。
CMDB APIは設定に関わるAPIですので、Ansibleで公開しているほとんどのモジュールはこのCMDB APIに変換されます。
ここでは、Monitor APIの一つであるファイアウォールポリシーのルックアップを実行するためのモジュールを作成します。

<h4>1. fortiosconfigモジュールを微調整</h4>
	sudo vi ~/40ansible/library/fortiosconfig.py
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
$ sudo vi ~/40ansible/library/fortiosconfig.py
...省略...
"MONITOR_CALLS = [
    'system config backup',
    'system config restore',
    'system resource usage',
    'system vdom-resource select',
    'system vmlicense upload',
    'vpn-certificate csr generate',
    'firewall policy-lookup select'
]"
...省略...
def _fortigate_monitor_get(data):
    login(data)

    functions = data['config'].split()

    path, name = extract_path_and_name(functions)
    parameters = data['config_parameters']

    resp = fos.monitor(path, name, vdom=data['vdom'], parameters=parameters)
    logout()
...省略...
</pre>

<h4>3. FNDN(https://fndn.fortinet.netで必須項目を確認</h4>
URI:  /api/v2/monitor/firewall/policy-lookup/select <br>
必須項目: srcintf, protocol, dest

<h4>4. プレイブック作成</h4>
	sudo vi fortigate_firewall_policy_lookup.yml
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
- hosts: localhost
#  strategy: debug
  vars:
   host: "192.168.122.40"
   username: "admin"
   password: "admin"
   vdom: "root"
  tasks:
  - name: firewall policy lookup
    fortiosconfig:
     config: "firewall policy-lookup select"
     action: "get"
     host: "{{ host }}"
     username: "{{ username }}"
     password: "{{ password }}"
     vdom: "{{ vdom }}"
     https: True
     ssl_verify: False
     config_parameters:
       srcintf: "port1"
       dest: "10.0.0.1"
       protocol: "tcp"
       sourceip: "5.5.5.5"
       destport: 443
</pre>

<h4>5. プレイブック実行</h4>
	ansible-playbook fortigate_firewall_policy_lookup.yml -vvv
<pre style="font-family:Courier New, Courier, monospace; color:#FFFFFF; background: #000000;">
…省略…
    },
    "meta": {
        "results": {
            "destip": "10.0.0.1",
            "policy_id": 1,
            "success": true
        },
        "status": "success",
        "version": "v6.2.1"
    }
}
</pre>
policy_id:1のポリシーがヒットしていることがわかります
<br><br>
