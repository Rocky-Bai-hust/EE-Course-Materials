# GitHub 上传操作指南

本文记录把本地资料目录发布到 GitHub 的最小流程。当前本地目录：

```text
E:\电信本科专业课程学习资料_Bai
```

## 1. 先准备 Git 身份

打开 PowerShell，执行下面两行。把尖括号里的内容替换成你自己的 GitHub 用户名和邮箱。

```bash
git config --global user.name "<你的GitHub用户名>"
git config --global user.email "<你的GitHub邮箱>"
```

如果你不想公开邮箱，可以在 GitHub 网页中打开：

```text
Settings -> Emails -> Keep my email addresses private
```

然后使用 GitHub 提供的 `数字+用户名@users.noreply.github.com` 邮箱。

## 2. 创建 GitHub 空仓库

1. 打开 GitHub 并登录。
2. 点击右上角 `+`，选择 `New repository`。
3. Repository name 建议填写：

```text
EE-Course-Materials
```

也可以使用中文名，但英文仓库名更利于分享和访问。

4. Description 可填写：

```text
电子信息类本科课程学习资料整理
```

5. 建议先选择 `Private`，确认没有版权或隐私问题后再改为 `Public`。
6. 不要勾选 `Add a README file`、`.gitignore`、`license`，因为本地已经准备好了。
7. 点击 `Create repository`。

## 3. 提交基础说明文件

进入资料目录：

```bash
Set-Location -LiteralPath 'E:\电信本科专业课程学习资料_Bai'
```

首次提交项目说明文件：

```bash
git add README.md NOTICE.md GITHUB_UPLOAD_GUIDE.md .gitignore
git commit -m "docs: initialize course materials repository"
```

## 4. 连接远程仓库并推送

把下面命令中的 `<你的用户名>` 替换成你的 GitHub 用户名。

```bash
git branch -M main
git remote add origin https://github.com/<你的用户名>/EE-Course-Materials.git
git push -u origin main
```

如果 GitHub 要求登录，按提示在浏览器中授权即可。

## 5. 分批上传课程资料

资料目录较大，不建议一次性 `git add .`。推荐按学期分批：

```bash
git add 大一上学期
git commit -m "add freshman fall course materials"
git push

git add 大一下学期
git commit -m "add freshman spring course materials"
git push
```

后续学期同理。这样即使某一步失败，也容易定位是哪个学期或哪个文件有问题。

## 6. 处理大文件

普通 GitHub 仓库不适合直接存放大型二进制资料。当前已经发现并忽略了若干超过 100MiB 的文件，例如大型安装包、教材 PDF、压缩包等。

建议处理方式：

- 教材扫描件、安装包、超大压缩包：不要上传到 Git 仓库。
- 自己整理的大文件：可以放到 GitHub Release、网盘，或使用 Git LFS。
- 如果只是为了备份完整资料，优先使用网盘或移动硬盘；GitHub 更适合作为索引、代码、笔记和中小型文档仓库。

本机已经安装 Git LFS。如确实需要上传少量大文件，可以先执行：

```bash
git lfs install
git lfs track "*.pdf"
git add .gitattributes
```

注意：不要轻易对整个资料目录的 PDF/PPT/压缩包全部启用 LFS。课程资料体量较大，LFS 也有存储和流量额度限制。

## 7. 公开前检查清单

公开仓库前至少检查：

- 是否包含姓名、学号、手机号、邮箱、成绩、聊天记录、截图隐私。
- 是否包含教师未授权课件、内部题库、考试原题、付费资料。
- 是否包含完整教材扫描件或明显来源于盗版资源的文件。
- 是否包含软件安装包、破解工具、序列号、账号密码。
- 是否有超过 100MiB 的文件仍被 Git 跟踪。

检查大文件命令：

```bash
git ls-files | ForEach-Object {
  $p = Join-Path (Get-Location) $_
  if (Test-Path -LiteralPath $p -PathType Leaf) {
    $f = Get-Item -LiteralPath $p
    if ($f.Length -gt 100MB) {
      "{0:N2} MB`t{1}" -f ($f.Length / 1MB), $_
    }
  }
}
```

如果这条命令有输出，说明还有不能直接推送到 GitHub 的大文件。
