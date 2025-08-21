---
title: 切换github账号中遇到的坑
categories: 
- github
layout: post
---
# 背景
工作需要，在mac命令行中切换github账号，始终未成功。push代码的时候提示“ERROR: Permission to xxx/xxx.github.io.git denied"

```
➜  xxx.github.io git:(main) git push -u origin main

ERROR: Permission to [account2]/xxx.github.io.git denied to [account1].
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

# 原因

1. 通过`ssh -T git@github.com`发现用github account1账号访问了github account2的代码仓库；
2. 通过`git config --list` 检查user.name是account2，符合预期；
3. 怀疑mac keychain中缓存问题，清理后无效；
4. 按照[Error: Permission denied (publickey)](https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey)检查不符合预期。`ssh-add -l -E sha256` 结果为空；怀疑是这个问题；
5. 按照[Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)，关键的一步是`ssh-add ~/.ssh/id_ed25519`

> Note: The --apple-use-keychain option stores the passphrase in your keychain for you when you add an SSH key to the ssh-agent. If you chose not to add a passphrase to your key, run the command without the --apple-use-keychain option.

*完整解决方案如下(第 2 步确认输出为空，第 3 步的 id_ed25519确认为需要提交的 github 账号对应的密钥)*

```
eval "$(ssh-agent -s)"
ssh-add -l -E sha256 
ssh-add  ~/.ssh/id_ed25519
```