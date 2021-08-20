# Custom Swift Development Setting

새로운 프로젝트를 시작할 때 마다 설정해두어야 하는 설정들

## .gitignore

* `.gitignore` 파일에 명시한 파일은 **git 변경 내역 추적에서 제외**한다.
* 협업 시 충돌을 방지하거나 불필요한 파일을 올리지 않을 수 있다.
* 주로 백업 파일, 로그 파일, 로컬 설정 파일 등이 포함된다.
* **XCode Swift 프로젝트에서는?** => *DS_Store, XCode Patch 관련, Dependency Manager 관련 등*
* `.gitignore` 파일을 추가하기 전에 commit을 했다면?
    ```shell
    git rm -r --cached [파일명]
    ```

```
# DS_Store
.DS_Store

# Pods 관련 (pod install or pod update 해서 자동 생성된 파일들) 
Podfile.lock
*.xcworkspace
Pods/

# xcodeproj 안의 불필요파일
*.xcuserstate
xcschememanagement.plist
contents.xcworkspacedata
```

### References

- [gitignore.io](https://gitignore.io)
- [CocoaPods 유용한 정보 모음입니다.](https://github.com/ClintJang/cocoapods-tips)

## Pods 프로젝트 경고 없애기

Podfile에서 "inhibit_all_warnings!" 만 추가하면 간단하게 경고가 사라집니다.

inhibit_all_warnings! 코드 추가

```
target '프로젝트명' do
  # 스위프트를 사용하지 않고 동적 라이브러리를 이용하지 않는다면 아래 구문을 주석처리 합니다
  use_frameworks!
  
  # xcode에서 pods의 모든 프로젝트 경고 없애기
  inhibit_all_warnings!

end
```

## SwiftLint

* **Swift 스타일 및 규칙을 적용하는 도구**
* 규칙에 어긋나는 코드 줄에는 Warning 또는 Error 발생

### SwiftLint 적용하기

* XCode File Navigator에서 `프로젝트 폴더` 클릭
* 기본 App `Target` 클릭
* `Build Phases`로 이동
* `+` 버튼 클릭
* `New Run Script Phases` 클릭하여 아래의 코드 추가

```shell
pod 'SwiftLint'
```

```shell
${PODS_ROOT}/SwiftLint/swiftlint
```

* AutoCorrect 기능까지 추가하고 싶을때

```shell
${PODS_ROOT}/SwiftLint/swiftlint autocorrect
```

* 방금 추가한 스텝의 위치를 `Compile Sources` 위로 이동한다.

### SwiftLint 규칙 설정

* 자기 스타일만의 규칙을 만들지 않으면 에러가 너무 많다.
* Root 경로에 `.swiftlint.yml` 파일 생성
* Pods 파일을 넣어주지 않으면, 오류가 생길 수도 있다. 

```yml
disabled_rules: # rule identifiers to exclude from running
- line_length
- colon
- comma
- control_statement
- switch_case_alignment
- vertical_whitespace
- trailing_whitespace

excluded: # paths to ignore during linting. Takes precedence over `included`.
- Pods
```

### References

* https://realm.github.io/SwiftLint/rule-directory.html
* https://github.com/realm/SwiftLint

---
