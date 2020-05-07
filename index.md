## AnsibleでFortiGateを動かしてみよう

---

<h2 id="pro"><font color="#2A7CB9">序. はじめに</font></h2>
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

<h2 id="lab0"><font color="#2A7CB9">LAB環境セットアップ</font></h2>
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
  
<h2 id="lab1"><font color="#2A7CB9">LAB1: ファイアウォールポリシーの設定</font></h2>
 <h4>1. 40ansibleディレクトリに入る</h4>
  $ cd ~/40ansible/<br>
 <h4>2. examplesディレクトリにあるfortigate_create_firewall_policy.ymlプレイブックをカレントディレクトリにコピー</h4>
  $ cp examples/fortigate_create_firewall_policy.yml .<br>
 <h4>3. fortigate_create_firewall_policy.ymlプレイブックを確認</h4>
  $ more fortigate_create_firewall_policy.yml<br>
 <p style="font-family:"Courier New", Courier, monospace; color:#FFFFFF; background: #000000;>
 $ more fortigate_create_firewall_policy.yml<br>
- hosts: localhost<br>
#  strategy: debug<br>
  vars:<br>
   host: ""192.168.122.40"<br>
   username: "admin"<br>
   password: "admin"<br>
   vdom: "root"<br>
  tasks:<br>
  - name: create firewall policy<br>
    fortiosconfig:<br>
     config: "firewall policy"<br>
     action: "set"<br>
     host: "{{ host }}"<br>
     username: "{{ username }}"<br>
     password: "{{ password }}"<br>
     vdom: ""{{ vdom }}""<br>
     https: False<br>
     ssl_verify: False<br>
--省略--<br>
 </p>
  <p>NOTE1: このプレイブックでは、fortiosconfigモジュールを使うことがわかります。Ansibleでは、モジュールを探索するためにプレイブックの置かれたディレクトリ内のlibraryディレクトリを参照します。2. でプレイブックをコピーしたのはこのためです。</p>
  <p>NOTE2: この演習では、まずお試しライセンスで行います。お試しライセンスでは、管理アクセスがhttpのみに限られますので、https: Falseとしています。</p>
 <h4>4. libraryディレクトリにfortiosconfigモジュールがあることを確認</h4>
  $ ls library<br>
 <h4>5. プレイブックを実行</h4>
  $ ansible-playbook fortigate_create_firewall_policy.yml<br>
 <h4>6. プレイブック実行後の設定確認</h4>
  $ ssh admin@192.168.122.40 show firewall policy<br>
  
  
<h2 id="lab2"><font color="#2A7CB9">LAB2: スタティックルートの設定</font></h2>
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
  
<h2 id="lab3"><font color="#2A7CB9">LAB3: ライセンスのアップロード</font></h2>
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

<h2 id="lab4"><font color="#2A7CB9">LAB4: スタティックURLフィルタの設定<font></h2>
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
  
<h2 id="lab5"><<font color="#2A7CB9">LAB5: Rolesの活用</font></h2>
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

<h2 name="lab6"><font color="#2A7CB9">LAB6: inventoryとhttpapiの活用</font></h2>
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
  
<h2 name="lab7"><font color="#2A7CB9">LAB7: ファイアウォールポリシーのルックアップ</font></h2>
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
