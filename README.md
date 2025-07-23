# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type-aware lint rules:

```js
export default tseslint.config({
  extends: [
    // Remove ...tseslint.configs.recommended and replace with this
    ...tseslint.configs.recommendedTypeChecked,
    // Alternatively, use this for stricter rules
    ...tseslint.configs.strictTypeChecked,
    // Optionally, add this for stylistic rules
    ...tseslint.configs.stylisticTypeChecked,
  ],
  languageOptions: {
    // other options...
    parserOptions: {
      project: ['./tsconfig.node.json', './tsconfig.app.json'],
      tsconfigRootDir: import.meta.dirname,
    },
  },
})
```

You can also install [eslint-plugin-react-x](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-x) and [eslint-plugin-react-dom](https://github.com/Rel1cx/eslint-react/tree/main/packages/plugins/eslint-plugin-react-dom) for React-specific lint rules:

```js
// eslint.config.js
import reactX from 'eslint-plugin-react-x'
import reactDom from 'eslint-plugin-react-dom'

export default tseslint.config({
  plugins: {
    // Add the react-x and react-dom plugins
    'react-x': reactX,
    'react-dom': reactDom,
  },
  rules: {
    // other rules...
    // Enable its recommended typescript rules
    ...reactX.configs['recommended-typescript'].rules,
    ...reactDom.configs.recommended.rules,
  },
})
```

## 修改地方

index.html

```js
  <script>
    $(function () {
      $('#Lionlogin').on('click', function () {
        const userAgent = navigator.userAgent
        const isLionB2CAPP = userAgent.includes('LionB2CAPP')

        if (isLionB2CAPP) {
          // App 環境的處理
          handleAppLogin()
        } else {
          // 網頁環境的處理
          showLightSpeedLoginLightboxWithCallback('chkMemberLightSpeedLogin')
        }
      })

      // 將 App 登入邏輯移到外面定義
      function handleAppLogin() {
        function isAndroid() {
          return /Android/i.test(navigator.userAgent)
        }

        function openAppNativeLoginPage() {
          const pageName = 'LoginPage'
          console.log('openPageInApp called')

          if (isAndroid()) {
            console.log('Detected as Android')
            if (window.Android && window.Android.openPage) {
              window.Android.openPage(pageName)
            } else {
              alert('Android interface not found')
            }
          } else {
            console.log('Detected as iOS')
            if (window.webkit && window.webkit.messageHandlers && window.webkit.messageHandlers.openPage) {
              console.log('Sending message to iOS')
              window.webkit.messageHandlers.openPage.postMessage(pageName)
            } else {
              alert('iOS interface not found')
            }
          }
        }

        openAppNativeLoginPage()
      }
    })
  </script>
```

App.tsx

```js
if (sMemberLogin) {
  if (user && result) {
    // post api 拿掉
    window.location.replace(`${configs.url.fullPath}?lu=${user}`)
  }
}
```

```js
 // 更新選項結果
  const updateAnswers = (ans: string[]) => {
    // 完成答題 立即計算結果
    if (newAnswers.length === questions.length) {
      setIsCalculating(true)
      try {
        const priorityOrder = resultsData.map((row) => row.result)
        const result = calcTestResult(answerList, priorityOrder)
        setResult(result)
        setCookie('testresult', result, 5)

        // post api 拿掉
      } catch (err: any) {
        console.error(err)
      } finally {
        setIsCalculating(false)
      }
    }
  }
```

ResultPage.tsx

```js
import { getCookieRegex } from '@/utils/utils'
import { submitResult } from '@/utils/api'

const [submitStatus, setSubmitStatus] = (useState < 'checking') | 'submitted' | ('failed' > 'checking')

useEffect(() => {
  const handleResultSubmission = async () => {
    const user = getCookieRegex('discern')
    const result = name // 使用傳入的結果名稱

    if (user && result) {
      try {
        const parsedResultData = parseResultData(resultsData)
        const resultInfo = parsedResultData[result]

        await submitResult({
          game: configs.game,
          discern: user,
          result: result,
          tags: resultInfo.tags.join(','),
        })

        setSubmitStatus('submitted')
        console.log(submitStatus)
      } catch (error) {
        setSubmitStatus('failed')
        console.error('結果提交失敗:', error)
      }
    }
  }

  handleResultSubmission()
}, [name])
```
