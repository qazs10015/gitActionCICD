# GitAction 自動部屬
透過 GitAction 做自動部屬，詳細步驟記錄在 `.github/workflows/main.yml`

需要產生一組 TOKEN 用於發佈，可於 `Setting => Developer settings => Personal access tokens` 產生，步驟如下：


![image](https://user-images.githubusercontent.com/30744341/149654371-3b28d084-981d-43f3-8c84-938b8690e244.png)

![image](https://user-images.githubusercontent.com/30744341/149654427-440e6af9-2c53-4d52-b48a-f7e0062ca078.png)

![image](https://user-images.githubusercontent.com/30744341/149654476-93904158-89e1-4939-a980-bfe26fcdc264.png)

有了 TOKEN 後就可以開始寫 .yml 拉💪

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

1. 定義環境變數 env，其底下包含 MY_SECRET、USER_NAME、USER_EMAIL

1. 需要在最新版的 ubuntu 系統下執行
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
> gh-pages 分支較為特別，為 gitPage 專用的分支，如果專案有此分支會自動發佈到 GitPages，且無法關閉 GitPages，詳情可見 [Unpublishing a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/unpublishing-a-github-pages-site#unpublishing-a-project-site)

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

