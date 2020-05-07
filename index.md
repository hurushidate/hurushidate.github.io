## AnsibleでFortiGateを動かしてみよう

---

<h2 id="pro">序. はじめに</h2>
 <h3>1. 40ansibleディレクトリに入る</h3>
  $ cd ~/40ansible/<br>
 <h3>2. examplesディレクトリにあるfortigate_create_firewall_policy.ymlプレイブックをカレントディレクトリにコピー</h3>
  $ cp examples/fortigate_create_firewall_policy.yml .<br>
 <h3>3. fortigate_create_firewall_policy.ymlプレイブックを確認</h3>
  $ more fortigate_create_firewall_policy.yml<br>
  <p>NOTE1: このプレイブックでは、fortiosconfigモジュールを使うことがわかります。Ansibleでは、モジュールを探索するためにプレイブックの置かれたディレクトリ内のlibraryディレクトリを参照します。2. でプレイブックをコピーしたのはこのためです。</p>
  <p>NOTE2: この演習では、まずお試しライセンスで行います。お試しライセンスでは、管理アクセスがhttpのみに限られますので、https: Falseとしています。</p>
 <h3>4. libraryディレクトリにfortiosconfigモジュールがあることを確認</h3>
  $ ls library<br>
 <h3>5. プレイブックを実行</h3>
  $ ansible-playbook fortigate_create_firewall_policy.yml<br>
 <h3>6. プレイブック実行後の設定確認</h3>
  $ ssh admin@192.168.122.40 show firewall policy<br>

<h2 id="lab0">LAB環境セットアップ</h2>
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
  
<h2 id="lab1">LAB1: ファイアウォールポリシーの設定</h2>
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
  
  
<h2 id="lab2">LAB2: スタティックルートの設定</h2>
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
  
<h2 id="lab3">LAB3: ライセンスのアップロード</h2>
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

<h2 id="lab4">LAB4: スタティックURLフィルタの設定</h2>
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
  
<h2 id="lab5">LAB5: Rolesの活用</h2>
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

<h2 name="lab6">LAB6: inventoryとhttpapiの活用</h2>
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
  
<h2 name="lab7">LAB7: ファイアウォールポリシーのルックアップ</h2>
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
