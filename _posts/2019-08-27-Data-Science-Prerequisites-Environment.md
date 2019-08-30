---
title: "Data Science - Python을 활용한 데이터 분석 환경 구성"
date: 2019-08-27 13:00
categories: development python data-science
---


## 1. 개발 환경 구성

* Jupyter는 웹 브라우저에서 파이썬 코드를 작성하고 실행할 수 있음

  ### 1-1. Anaconda 설치

  * 아나콘다(Anaconda)는 패키지 관리와 디플로이를 단순케 할 목적으로 과학 계산(데이터 과학, 기계 학습 애플리케이션, 대규모 데이터 처리, 예측 분석 등)을 위해 파이썬과 R 프로그래밍 언어의 자유-오픈 소스 배포판이다. 패키지 버전들은 패키지 관리 시스템 conda를 통해 관리된다.
  * https://www.anaconda.com/distribution/#download-section
  * Version: Anaconda3 for Python 3.7 version
  * 설치 중간에 'Advnced Options' 단계에서 'Add Anaconda to my PATH environment variable' 옵션 체크 (혹은 설치 완료 후 직접 환경 변수 설정)
  * cmd에서 `conda` 명령어 입력하여 설치 완료 확인

  ### 1-2. Virtual 환경 구성
  1. cmd > `conda create --name kaggle python=3.6` 입력하여 환경 구성

     ```
     wincertstore-0.2     | 14 KB     | 
     python-3.6.9         | 20.4 MB   | 
     wheel-0.33.4         | 57 KB     | 
     pip-19.2.2           | 1.9 MB    | 
     setuptools-41.0.1    | 521 KB    | 
     certifi-2019.6.16    | 156 KB    | 
     ```

  2. cmd > `active kaggle` 입력하여 virtual 환경 진입

  ### 1-3. Python Scientific Libraries 설치
  1. *(3-2에 이어서)* cmd 실행 > virtual 환경 진입
  2. `pip install jupyter numpy pandas matplotlib seaborn scikit-learn scipy plotly cufflinks tqdm`

  ### 1-4. 기타 라이브러리 설치 (XGBoost, LightGBM, graphviz, python-graphvis)
  1. (3-2에 이어서)* cmd 실행 > virtual 환경 진입

  2. `conda install -c anaconda py-xgboost`
  3. `conda install -c conda-forge lighthgbm graphviz python-graphviz`

  ### 1-5. virual 환경 사용을 위한 IPython 커널 등록

  1. (3-2에 이어서)* cmd 실행 > virtual 환경 진입
  2. `pip install ipykernel``
  3. ``python -m ipykernel install --user --name kaggle --display-name "kaggle"`

  ### 1-6. Jupyter notebook 실행

  1. (3-2에 이어서)* cmd 실행 > virtual 환경 진입
  2. `jupyter notebook`



## 2. IPython

* Interactive Python. python을 실행할 수 있는 웹 기반의 쉘.
* A powerful interactive shell.
* A kernel for [Jupyter](https://jupyter.org/).
* Support for interactive data visualization and use of [GUI toolkits](http://ipython.org/ipython-doc/stable/interactive/reference.html#gui-event-loop-support).
* Flexible, [embeddable interpreters](http://ipython.org/ipython-doc/stable/interactive/reference.html#embedding-ipython)
* Easy to use, high performance tools for [parallel computing](https://ipyparallel.readthedocs.io/en/latest/).

  #### 2-1. Magic commands

  * `%timeit`: 실행 시간 측정
    * %: line magic
    * %%: cell magic
  * `%history`: 히스토리
  * `%%writefile [filename]`: 셀의 내용을 파일에 쓰기
  * `%run`: 파일 실행
  * *추가)* 가장 전통적인 그래프 라이브러리인 `matplotlib` 사용 시 노트북 위에서 그래프를 보여주는 기능을 제공하지 않기 때문에 magic command를 쓰면 그래프를 직접 확인할 수 있다. : `%matplotlib`

  #### 2-2. Shell Commands

  * `!` 를 앞에 붙여주면 CLI 사용 가능 (ex: `!pip --version` or `!date`)
  * 자주 쓰이는 커맨드는 `!` 없이도 사용 가능 (ex: `cd ..` or `dir`)

  #### 2-3. Help&Tab completion

  * `tab` key를 누르면 코드 자동 완성이 됨
  * `shift`+`tab` key를 누르면 Help 말풍선이 나타남
  * `shift`+`tab`+`tab` key를 누르면 Help 말풍선이 확대됨
  * `?`를 붙이고 실행(`shift`+`enter`)를 누르면 signature를 볼 수 있음 (ex: `range?`)

  #### 2-4. 이전 명령어 & 결과 보기
  
  * `_`: 직전 결과
  * `__`: 전전 결과

  #### 2-5. pdf
  
  * `%debug` 명령어를 통해 디버깅 모드에 진입할 수 있음
  * `%pdb on`: pdb 자동 실행
  * `%pdb off`: pdb 자동 실행 종료
  * pdb 명령어:
    * `help`: 도움말
    * `next`: 다음 문장으로 이동
    * `print`: 변수값 화면에 표시
    * `list`: 소스코드 리스트 출력. 현재 위치 화살표로 표시됨
    * `where`: 콜스택 출력
    * `continue`: 계속 실행. 다음 중단점에 멈추거나 중단점 없으면 끝까지 실행
    * `step`: Step Into 하여 함수 내부로 들어감
    * `return`: 현재 함수의 리턴 직전까지 실행
    * `!변수명`: 값 변수에 값 재설정
    * `up`: 한 프레임 위로
    * `down`: 한 프레임 아래로
    * `quit`: pdb 종료

      

## 3. Jupyter Notebook

* Visualized한 결과물을 위해 사용
* Language agnostic(언어에 상관 없는): IPython은 Jupyter의 커널 중 하나일 뿐
* Youtube, audio 삽입이 자유롭고 Markdown, HTML, Latex 등등 사용 가능
* 커널 컨트롤이 가능 (interrupt: `ii`, restart: `00`)
* Widget 제공: `interact`를 통한 위젯 사용

  ### 3-1. Shortcut

  - `enter` key를 눌러 editor 모드 진입
  - `esc` key를 눌러 editor 모드 해제
  - `ctrl`+`enter` key를 눌러 코드 실행
  - `shift`+`enter` key를 눌러 코드 실행 후 다음라인으로 전환



## 4. Conda

* 패키지, 디펜던시, 가상 환경 관리의 끝판왕 - Python부터 R, Ruby, Lua, Scala, Java, JavaScript, C/ C++, FORTRAN 등등

  #### 4-1. pip vs conda

  * **pip**
    - 파이썬 패키지 관리자
    - 파이썬만 관리. 파이썬 너머에 의존성이 있는 경우는 관리하지 못함
  * **conda**
    - 패키지 관리자이자 가상환경 관리자
    - 언어 상관 없이 모두 관리. 파이썬도 패키지 중 하나일 뿐.
    - 패키지 관리 뿐 아니라 가상 환경 생성 및 관리도 가능하다.
    - 파이썬으로 쓰여짐

  #### 4-2. 가상 환경 관리

   * 새 가상 환경 만들기
    * `conda create --name tensorflow`
      	* `conda create --name tensorflow python`: 파이썬만 있는 깨끗한 환경
       	* `conda create --name tensorflow python=2.7`: 파이썬 버전 지정
   * 가상 환경 진입
      * `source active tensorflow`
   * Jupyter에 새 kernel 등록
      * `pip install ipykernel`
        	* `python -m ipykernel install --user --name tensorflow --display-name "Python (TensorFlow)"`
   * 가상 환경 빠져나오기
      * `source deactivate`
   * 가상 환경 목록 보기
      * `conda env list`
   * 설치된 패키지 목록 보기
      * `conda list`
