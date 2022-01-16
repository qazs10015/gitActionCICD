# GitAction 自動部屬
透過 GitAction 做自動部屬，詳細步驟記錄在 `.github/workflows/main.yml`

[收費方式可見內文](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions)
> 如果 repository 為 public 就不用錢! (2022/01/16)
---

首先，需要產生一組 TOKEN 用於發佈，可於 `Setting => Developer settings => Personal access tokens` 產生，步驟如下：


![image](https://user-images.githubusercontent.com/30744341/149654371-3b28d084-981d-43f3-8c84-938b8690e244.png)

![image](https://user-images.githubusercontent.com/30744341/149654427-440e6af9-2c53-4d52-b48a-f7e0062ca078.png)

> NOTE： 產生出來的 TOKEN `只顯示一次` 請妥善保存，日後設定權限會用到

![image](https://user-images.githubusercontent.com/30744341/149655778-e1303ac0-3073-4039-972e-828d614fe47c.png)

回到 `repository => Setting => Secrets`，點選 New repository secret
![image](https://user-images.githubusercontent.com/30744341/149655931-0501d0e9-aec8-4602-8a7e-4d77f4d627b2.png)

輸入一個 secret Name 和剛剛取得的 token

![image](https://user-images.githubusercontent.com/30744341/149656328-7a117995-6ebd-4ff6-b365-3e973c2c2c53.png)

新增成功後就會顯示

![image](https://user-images.githubusercontent.com/30744341/149656271-354771d0-ebe2-4957-a854-ae577dbe2317.png)



完成以上專案設定的步驟後後就可以開始寫 .yml 拉💪

---
以下簡單敘述此專案的 .yml ，整個 workflow 可以有多個 jobs，每個 jobs 下也可以有多個動作

描述什麼時候觸發 workflow

``` yml
on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: [master]
  pull_request:
    branches: [master]
```

1. 此 workflow 下有一個 jobs 叫做 build

2. 定義環境變數 env，其底下包含 MY_SECRET、USER_NAME、USER_EMAIL

3. 需要在最新版的 ubuntu 系統下執行
``` yml
jobs:
  # This workflow contains a single job called "build"
  build:
    env:
      MY_SECRET: ${{secrets.DEPLOY_TOKEN}}
      USER_NAME: junyu
      USER_EMAIL: happystyle666@gmail.com
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
```
1. 執行 `npm install`，先安裝需要的套件

1. 執行 `npm run build`，編譯並建置要發佈的檔案

1. 完成後切到放置檔案的資料夾，本專案為 `./docs` 
1. 進行 git 初始化，新增分支 `gh-pages`，設定相關 git 發佈資訊
> gh-pages 分支較為特別，為 GitPages 專用的分支，如果專案有此分支會自動發佈到 GitPages，且無法關閉，詳情可見 [Unpublishing a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/unpublishing-a-github-pages-site#unpublishing-a-project-site)

![image](https://user-images.githubusercontent.com/30744341/149655218-b96a33a4-18e5-4897-b139-25f31d773800.png)


``` yml
 - name: install
        run: npm install

      - name: build
        run: npm run build

      - name: commit file
        run: |
          cd ./dist
          git init -b gh-pages
          git config --local user.name $USER_NAME
          git config --local user.email $USER_EMAIL
          git remote add origin https://$MY_SECRET@github.com/$GITHUB_REPOSITORY.git
          git add .
          git commit -m "deploy to Github pages"
          git push origin gh-pages -f
          echo 🤘 deploy gh-pages complete.
```

Reference: [深入但不淺出，如何用 github actions 自動發佈 gh-pages](https://milkmidi.medium.com/%E6%B7%B1%E5%85%A5%E4%BD%86%E4%B8%8D%E6%B7%BA%E5%87%BA-%E5%A6%82%E4%BD%95%E7%94%A8-github-actions-%E8%87%AA%E5%8B%95%E7%99%BC%E4%BD%88-gh-pages-8183464dfe84)
