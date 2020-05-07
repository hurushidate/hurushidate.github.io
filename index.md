## AnsibleでFortiGateを動かしてみよう

---

<h2 id="pro"><font color="#9164CC">序. はじめに</font></h2>
 <h4>1. 40ansibleディレクトリに入る</h4>
  $ cd ~/40ansible/<br>
 <h4>2. examplesディレクトリにあるfortigate_create_firewall_policy.ymlプレイブックをカレントディレクトリにコピー</h4>
  $ cp examples/fortigate_create_firewall_policy.yml .<br>
 <h4>3. fortigate_create_firewall_policy.ymlプレイブックを確認</h4>
  $ more fortigate_create_firewall_policy.yml<br>
  <p>NOTE1: このプレイブックでは、fortiosconfigモジュールを使うことがわかります。Ansibleでは、モジュールを探索するためにプレイブックの置かれたディレクトリ内のlibraryディレクトリを参照します。2. でプレイブックをコピーしたのはこのためです。</p>
  <p>NOTE2: この演習では、まずお試しライセンスで行います。お試しライセンスでは、管理アクセスがhttpのみに限られますので、https: Falseとしています。</p>
 <h4>4. libraryディレクトリにfortiosconfigモジュールがあることを確認</h4>
  $ ls library<br>
 <h4>5. プレイブックを実行</h4>
  $ ansible-playbook fortigate_create_firewall_policy.yml<br>
 <h4>6. プレイブック実行後の設定確認</h4>
  $ ssh admin@192.168.122.40 show firewall policy<br>

<h2 id="lab0"><font color="#9164CC">LAB環境セットアップ</font></h2>
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
