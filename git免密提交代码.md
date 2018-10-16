## git http免密登录

### 1.在命令行输入命令:

  git config --global credential.helper store

这一步会在用户目录下的.gitconfig文件最后添加：

  [credential]

      helper = store

### 2.push 代码push你的代码(git push), 这时会让你输入用户名和密码, 这一步输入的用户名密码会被记住, 
下次再push代码时就不用输入用户名密码!这一步会在用户目录下生成文件.git-credential记录用户名密码的信息。
