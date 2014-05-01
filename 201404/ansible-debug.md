Ansibleの問題を発見したが、結局Pull Requestを出すまでに至らなかった。調べたことをそのまま捨てるのも悲しいので記録を残しておく。

## 発見した問題

Ansibleのプレイブックはyamlファイルである。例えば次のスニペットは`/etc/selinux/config`に`SELINUX`という文字列が存在しない場合、`SELINUX=disabled`という行を追加する[1]。

```
- name: foo bar
  lineinfile: dest=/etc/selinux/config regexp=^SELINUX= line=SELINUX=disabled
```

ここで`line=`にタブ文字を含めようとしてナイーブにハードタブを入れると`AnsibleError: Invalid control character at: line xxx column xxx (char xxx)`のようなエラーになる。

## Pull Requestを諦めた理由

GitHubのAnsibleリポジトリでこの問題が既に言及されており、workaroundが紹介されていたため[2]。

## Ansibleリポジトリに関するメモ

Ansibleリポジトリをテストできるようにするため、次のパッケージを`pip`でインストールする。

```
pip install nose
pip install passlib
```

これらがインストールされた状態でAnsibleリポジトリのルートディレクトリに移動すると`make tests`が通るようになる。

元々の問題であるタブがパースできない問題は`lib/ansible/utils/__init.py__`で定義されている`parse_yaml`に手を入れることで解消される。`json.loadd(data)`に引数を追加し`json.loads(data, strict=False)`にするとタブ文字も含めてプレイブックをパースできるようになる。

ただし、`strict=False`は他にも副作用があるし、既にworkaroundが公式リポジトリのissueで言及されているので、この変更をPull Requestするのは諦めた。

何かの記録として、作業メモをここに残しておく。

* [1] [lineinfile - Ensure a particular line is in a file, or replace an existing line using a back-referenced regular expression. &mdash; Ansible Documentation](http://docs.ansible.com/lineinfile_module.html)
* [2] [Impossible to use tabs · Issue #1789 · ansible/ansible](https://github.com/ansible/ansible/issues/1789)
