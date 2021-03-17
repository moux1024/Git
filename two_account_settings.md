# 设置多个git域名下的账户自动切换
1. 清除global设置  
  ```bash
  git config --global --unset user.name
  git config --global --unset user.email
  ```
2. 在~/.ssh文件夹创建对应域名子文件夹 
  ```bash
  cd ~/.ssh
  mkdir hostname1
  mkdir hostname2
  ```
3. 用账户邮箱在对应文件夹生成密钥 
  ```bash
  ssh-keygen -t rsa -C "your_email@hostname1.com"
  # Enter file in which to save the key: /Users/yourusername/.ssh/hostname1
  ssh-keygen -t rsa -C "your_email@hostname2.com"
  # Enter file in which to save the key: /Users/yourusername/.ssh/hostname2
  ```
4. 将公钥添加到git服务器
  ```bash
  # 打开复制也行
  pbcopy <hostname1.pub
  ```
5. 在~/.ssh创建config文件，保存以下内容
  ```bash
    # The git info for company
    Host  gitlab.hostnam1.net
    HostName  gitlab.hostnam1.net
    User yourname@hostnam1.com
    IdentityFile ~/.ssh/hostnam1/hostnam1
    # The git info for github
    Host github.com
    HostName github.com
    User yourname
    IdentityFile ~/.ssh/github/github
  ```
 6. 最后，将密钥添加到ssh-agent
  ```bash
  ssh-add ~/.ssh/hostname1/id_rsa_hostname1
  ```
