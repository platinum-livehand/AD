## 如何生成自己的token

1. 在个人设置页面，找到 **Settings**

![](..\picture\git push-1.png)

2. 选择开发者设置 Developer settings

![](..\picture\git push-2.png)

3. 选择个人访问令牌 Person acces tokens ，然后选中生成令牌。

![](..\picture\git push-3.png)

4. 设置 token 的有效期，访问权限。

![](..\picture\git push-4.png)

5. 生成令牌 Generate token

![](..\picture\git push-5.png)

![](..\picture\git push-6.png)

**注意保留生成的token，因为它只在生成时可见，后续无法看见，忘记了只能重新生成。**

6. 使用自己生成的 token 直接添加远程仓库链接中，这样就可以避免同一个仓库每次提交代码都需要输入 token 。

```bash
git remote set-url origin https://<your_token>@github.com/<USERNAME>/<REPO>.git
```

