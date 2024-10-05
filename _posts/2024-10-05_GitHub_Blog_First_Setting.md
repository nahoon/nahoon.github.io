---
layout: post
title: "GitHub Pages와 Jekyll을 사용한 블로그 세팅 및 오류 해결 과정"
date: 2024-10-05
categories: [Blog, GitHub, Jekyll]
tags: [GitHub Pages, Jekyll, Ruby, 오류 해결]
---

GitHub Pages와 Jekyll을 사용하여 개인 블로그를 만들 때, 초기 설치 과정에서 여러 문제와 오류가 발생할 수 있습니다. 이번 글에서는 **GitHub Pages**와 **Jekyll**을 기반으로 블로그를 처음 설정하고, 다양한 오류를 해결한 과정을 기록합니다.

## 1. GitHub Pages와 Jekyll 설정

먼저 GitHub Pages와 Jekyll을 사용하여 블로그를 설정하기 위한 기본 설치부터 시작했습니다. GitHub Pages는 Jekyll을 이용해 정적 사이트를 쉽게 만들고 배포할 수 있도록 도와줍니다.

**기본적인 설정 과정**은 다음과 같습니다. (검색을 통해 쉽게 확인 가능하므로 간단히 작성합니다):

1. **GitHub Pages 리포지토리 생성**
   - GitHub에서 `username.github.io` 형식으로 새로운 리포지토리를 생성합니다.
   - GitHub Pages를 활성화하고, 해당 리포지토리를 통해 블로그를 배포합니다.

2. **Jekyll 테마 설정**
   - Jekyll 테마 중 하나인 **Minimal Mistakes**를 선택하였습니다.
   - `_config.yml` 파일에서 블로그의 제목, 설명, 언어 및 로케일을 설정합니다.

```yaml
title: "내 블로그"
description: "블로그 설명"
locale: "ko"
lang: "ko"
timezone: "Asia/Seoul"
```

이렇게 설정하면 블로그는 한국어를 기본 언어로 사용하고, 한국 시간대에 맞춰 표시됩니다.

## 2. Ruby와 Bundler로 Jekyll 실행하기

Jekyll은 Ruby 기반으로 작동하므로, 이를 실행하려면 **Ruby**와 **Bundler**가 필요합니다. 다음은 Ruby와 Bundler를 설치한 후 Jekyll을 실행하는 과정입니다.

1. **Ruby 및 Bundler 설치**

   Ruby를 설치한 후, Bundler를 설치하여 Jekyll 관련 의존성을 관리합니다.

   ```bash
   gem install bundler
   ```

2. **의존성 설치**

   프로젝트 디렉토리에서 `Gemfile`에 정의된 의존성을 설치하기 위해 `bundle install` 명령어를 실행합니다.

   ```bash
   bundle install
   ```

3. **Jekyll 서버 실행**

   로컬 환경에서 블로그를 미리보기 위해 Jekyll 서버를 실행합니다.

   ```bash
   bundle exec jekyll serve
   ```

이 명령어를 통해 로컬에서 `http://localhost:4000`에서 블로그를 확인할 수 있습니다.

## 3. 오류 발생 및 해결 과정

블로그를 설정하는 과정에서 다양한 오류가 발생할 수 있습니다. 실제로 몇 가지 주요 오류를 겪었고, 이를 해결한 방법을 아래에 기록합니다.

### 오류 1: `bundler: command not found: jekyll`

```bash
bundler: command not found: jekyll
Install missing gem executables with `bundle install`
```

이 오류는 Jekyll이 설치되지 않았거나, Bundler가 Jekyll을 인식하지 못할 때 발생합니다. 이를 해결하기 위해 Jekyll과 Bundler를 설치합니다.

```bash
gem install jekyll bundler
```

### 오류 2: `MSYS2 seems to be unavailable`

```bash
MSYS2 seems to be unavailable
Verify integrity of msys2-x86_64-20221028.exe ... Failed
```

이 오류는 **MSYS2** 설치 중에 발생한 문제로, MSYS2 설치 파일이 더 이상 사용할 수 없는 경우 발생합니다. MSYS2는 Ruby와 함께 필요한 패키지를 설치하는 도구이므로, 이를 해결하기 위해 수동으로 MSYS2를 설치하거나, **RubyInstaller**와 함께 제공되는 **Devkit**을 설치했습니다.

- **MSYS2 수동 설치**: [MSYS2 공식 사이트](https://www.msys2.org/)에서 최신 설치 파일을 다운로드합니다.
- **RubyInstaller + Devkit 설치**: RubyInstaller를 사용하여 MSYS2와 Devkit을 함께 설치할 수 있습니다.

### 오류 3: `Gem::UnknownCommandError: Unknown command jekyll`

```bash
ERROR: While executing gem ... (Gem::UnknownCommandError)
Unknown command jekyll
```

이 오류는 `jekyll` 명령어를 RubyGems가 인식하지 못할 때 발생합니다. Jekyll이 제대로 설치되지 않았을 수 있습니다. 이를 해결하기 위해 Jekyll을 다시 설치한 후 `bundle exec` 명령어로 Jekyll을 실행합니다.

```bash
gem install jekyll
bundle exec jekyll serve
```

### 오류 4: `tzinfo` 관련 오류

```bash
Dependency Error: Yikes! It looks like you don't have tzinfo or one of its dependencies installed.
```

이 오류는 시간대(time zone) 처리에 필요한 `tzinfo` Gem이 누락되어 발생한 문제입니다. 이를 해결하기 위해 Gemfile에 `tzinfo`와 `tzinfo-data`를 추가하고, `bundle install` 명령어를 실행합니다.

```ruby
gem "tzinfo"
gem "tzinfo-data"
```

```bash
bundle install
```

### 오류 5: `minimal-mistakes-jekyll.gemspec: No such file or directory @ rb_sysopen - package.json`

```bash
[!] There was an error while loading `minimal-mistakes-jekyll.gemspec`: No such file or directory @ rb_sysopen - package.json
```

이 오류는 Minimal Mistakes 테마 설치 시 `package.json` 파일을 찾지 못해 발생한 문제입니다. 이 오류는 빌드 결과물 디렉토리인 `_site`에서 명령어를 실행할 때 발생할 수 있습니다. Jekyll 명령어는 프로젝트 루트 디렉토리에서 실행해야 합니다.

- **해결 방법**: 프로젝트 루트 디렉토리에서 명령을 실행하고, `_site` 디렉토리는 수정하지 않습니다.

```bash
cd /path/to/your/project
bundle exec jekyll serve
```

또한, **Gemfile**에 `minimal-mistakes-jekyll`을 추가하여 테마 의존성을 다시 설치했습니다.

```ruby
gem "minimal-mistakes-jekyll"
```

## 4. 경고 처리

Ruby 3.4.0부터 일부 표준 라이브러리가 기본 Gem에서 제외되기 때문에, `csv` 및 `base64` 라이브러리를 Gemfile에 추가해야 합니다. 이를 추가하지 않으면 경고 메시지가 표시됩니다.

```ruby
gem "csv"
gem "base64"
```

이를 통해 미래의 Ruby 버전에서도 발생할 수 있는 경고를 예방할 수 있습니다.

## 5. 마무리 및 블로그 실행

모든 오류와 경고를 해결한 후, Jekyll을 정상적으로 실행할 수 있었습니다. Jekyll 서버를 다시 실행하여 로컬에서 블로그를 확인할 수 있습니다.

```bash
bundle exec jekyll serve
```

이제 `http://localhost:4000`에서 블로그를 미리볼 수 있으며, 수정 사항을 반영하면서 블로그를 꾸밀 수 있습니다.

---

### 결론

GitHub Pages와 Jekyll을 이용한 블로그 설정 과정에서 발생할 수 있는 여러 오류와 문제를 해결하면서 블로그를 성공적으로 설정할 수 있었습니다. Ruby, Bundler, MSYS2 등의 도구를 사용하여 Jekyll 블로그를 관리하고, 필요한 의존성을 해결하면서 블로그를 완성할 수 있었습니다.

블로그 설정 과정에서 발생하는 다양한 오류는 자연스러운 과정이지만, 차근차근 문제를 해결해 나가면 누구나 GitHub Pages와 Jekyll을 활용한 멋진 블로그를 만들 수 있습니다.

---

**참고 리소스**:
- [Jekyll 공식 문서](https://jekyllrb.com/)
- [MSYS2 공식 사이트](https://www.msys2.org/)
- [RubyInstaller 공식 다운로드](https://rubyinstaller.org/downloads/)
- [Minimal Mistakes GitHub 페이지](https://github.com/mmistakes/minimal-mistakes)
