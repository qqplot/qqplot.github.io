---
layout: post
title:  "M1 Mac에서 tensorflow와 transformers 설치하기"
categories: [ datascience, nlp ]
tags: [ m1, tensorflow, anaconda, miniforge, huggingface, transformers ]
fullview: false
comments: true
---



M1 Mac을 사용하니 설치부터 도전이다. 자연어처리를 하기 위해 tensorflow와 transformers를 설치해보자.

---

<br/>

### M1 Mac은 다르다

높은 배터리 지속성과 낮은 발열, 무엇보다도 합리적인 가격에 맥북을 쓸 수 있다는 것에 홀딱 반해 M1을 구매했지만, 아무래도 아직은 소수 커뮤니티이다 보니 불편한 점이 있다. M1 Mac은 ARM64 칩을 사용한다. 우리가 흔히 쓰는 인텔 칩이나 x86 와는 다르게 작동하므로 소프트웨어도 그에 맞는 것을 설치해야 한다. 



물론 이 불편함이 즐겁기도 하다. 얼마나 내가 하드웨어에 대해서 관심이 없었는지를 잘 알게 해준다. 운영체제를 바꾸는 순간 Window 유저였던 나의 생산성이 크게 떨어지는 경험도 했다. 자판부터 달라졌고 커맨드도 다시 외워야 했다. 키보드 하나 고르기도 까다로웠다. 공식 홈페이지에 있는 매직 키보드는 너무 비싸다.. 



아무튼 HuggingFace의 transformer를 사용하기 위해 tensorflow를 먼저 설치해보자.



<br/>

### Mac OS용 Tensorflow 설치  

tensorflow 2.5부터 M1 맥북의 GPU를 사용할 수 있는 plugin이 생겼다. `tensorflow-metal`이다. GPU를 통해서 머신러닝 학습 속도를 가속화할 수 있게 되었다! 

macOS 12.0+ 이상의 업그레이드가 필요하다. 아직까지는 multi-GPU는 사용안되고 1개의 GPU만 사용 가능하다. (~~나는 왜 GPU 풀 옵션으로 주문했던가..~~)

현재 나는 BigSur에서 Monterey로 업데이트가 되어 있다.

![mac_os_version.png](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/mac_os_version.png?raw=true)

<br/>

<br/>



#### STEP 1: Xcode command line tools 설치

```sh
xcode-select --install
```



 <br/>

<br/>



#### STEP 2: Miniforge (Anaconda) 설치

일반 Anaconda는 안되고 ARM64용 miniforge를 설치해야 한다. [링크](https://developer.apple.com/metal/tensorflow-plugin/)에서 Download를 받고 해당 명령어를 실행하도록 한다.

<br/>

![miniforge_down.png](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/miniforge_down.png?raw=true)

<br/>

```sh
chmod +x ~/Downloads/Miniforge3-MacOSX-arm64.sh
sh ~/Downloads/Miniforge3-MacOSX-arm64.sh
source ~/miniforge3/bin/activate
```

<br/>

✓ *이미 mini forge가 설치되어 있다면 제거하고 설치하자.* [링크](https://github.com/conda-forge/miniforge)

생각보다 mac에서는 conda를 지우는 것이 만만치가 않다. 응용프로그램에도 없기도 하고... conda uninstall이나 clean 패키지를 설치하는 방법은 통하지 않았다. 아무래도 homebrew를 통해 설치를 해서 그런 듯 하다. 위 링크 방법대로 하니 잘 지워졌다.

<br/>

<br/>

#### STEP 3: Conda 가상환경 생성 및 패키지 설치

자 miniforge3를 설치했다면 가상환경을 만들고 활성화한다.

```sh
# Create an environment
conda create --name nlp python=3.8

# Activate an environment
conda activate nlp
```



가상환경 위에 tensorflow를 설치하자. Window에서의 패키지명과 다르니 주의하자. Tensorflow dependency를 설치한 후에 본 설치를 하고, metal plugin을 설치하는 순서이다.



```sh
# Dependency
conda install -c apple tensorflow-deps

# Tensorflow
pip install tensorflow-macos

# Metal plugin
pip install tensorflow-metal
```



참고로 패키지를 제거하는 명령어는 다음과 같다.

```sh
# uninstall existing tensorflow-macos and tensorflow-metal
python -m pip uninstall tensorflow-macos
python -m pip uninstall tensorflow-metal
# Upgrade tensorflow-deps
conda install -c apple tensorflow-deps --force-reinstall
# or point to specific conda environment
conda install -c apple tensorflow-deps --force-reinstall -n my_env
```



tensorlfow가 잘 설치되었는지 확인해보자.

```python
import tensorflow as tf

print(tf.__version__)

# 2.7.0
```



tensorflow가 설치가 제대로 되었다면 HuggingFace의 transformers도 설치하자

```sh
conda install -c huggingface transformers
```



그리고 주요 공통 패키지도 설치한다.

```sh
conda install -c conda-forge scikit-learn
conda install -c conda-forge pandas
conda install -c conda-forge jupyterlab
```

<br/><br/>



#### STEP 4: MNIST로 테스트 해보기

설치가 잘 되었다면 tensorflow에서 GPU를 잘 인식하는지 테스트해 본다.

```
conda create --name nlp python=3.8

conda activate nlp
```



다음과 같이 Tensorflow 패키지에서 config를 확인해볼 수 있다.

```python
print("Num GPUs Available: ",
      len(tf.config.experimental.list_physical_devices('GPU')))

# Num GPUs Available:  1
```



자, 이제 실제로 머신러닝 모델을 돌려보고 GPU 자원을 모니터링해보자.

MNIST 예시 코드는 다음과 같다.

```python
%%time 
import tensorflow.compat.v2 as tf
import tensorflow_datasets as tfds

tf.enable_v2_behavior() 

from tensorflow.python.framework.ops import disable_eager_execution

disable_eager_execution()

(ds_train, ds_test), ds_info = tfds.load( 
    'mnist', 
    split=['train', 'test'], 
    shuffle_files=True, 
    as_supervised=True, 
    with_info=True, 
)

def normalize_img(image, label):
    """ Normalizes images: 'unit8' -> 'float32'."""
    return tf.cast(image, tf.float32) / 255., label

batch_size = 128 
ds_train = ds_train.map( normalize_img, num_parallel_calls=tf.data.experimental.AUTOTUNE) 
ds_train = ds_train.cache() 
ds_train = ds_train.shuffle(ds_info.splits['train'].num_examples) 
ds_train = ds_train.batch(batch_size) 
ds_train = ds_train.prefetch(tf.data.experimental.AUTOTUNE)


ds_test = ds_test.map( normalize_img, num_parallel_calls=tf.data.experimental.AUTOTUNE)
ds_test = ds_test.batch(batch_size) 
ds_test = ds_test.cache() 
ds_test = ds_test.prefetch(tf.data.experimental.AUTOTUNE)

model = tf.keras.models.Sequential([ 
    tf.keras.layers.Conv2D(32, kernel_size=(3, 3), activation='relu'),
    tf.keras.layers.Conv2D(64, kernel_size=(3, 3), activation='relu'), 
    tf.keras.layers.MaxPooling2D(pool_size=(2, 2)), 
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation='relu'), 
    tf.keras.layers.Dense(10, activation='softmax') 
]) 

model.compile( loss='sparse_categorical_crossentropy', optimizer=tf.keras.optimizers.Adam(0.001), metrics=['accuracy'], ) 
model.fit( ds_train, epochs=12, validation_data=ds_test, )


```

<br/>

코드를 돌리면 metal plugin이 정상적으로 호출되고 있음을 알 수 있다.

![status_using_m1.png](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/status_using_m1.png?raw=true)



<br/>

<br/>

학습을 진행하는 동안 Activity Monitor를 통해 GPU 자원활용 현황을 살펴보자. Python 3.8 프로세스가 97.8%를 점유하여 사용하고 있다. 잘 설치가 되었다!



![activity-monitor.png](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/activity-monitor.png?raw=true)





<br/>



총 학습 시간은 1분 55초이다. 이 속도는 colab GPU보다는 2배, None보다는 15배 빠르다고 한다. ([링크](https://gmnam.tistory.com/271))



![elapse_time.png](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/elapse_time.png?raw=true)



<br/>

드디어 환경세팅이 되었다.. 회사에서 쓰고 있는 인스턴스 비용이 아까워서 로컬에서 작업해보려고 하다가 여기까지 왔다. 환경설정 말고는 아무 것도 안 했는데 지친 기분이다. 이제.. 다시 Transformers를 연습하러 가자.









<br/>

### 참고

- [[M1 맥북] GPU에서 tensorflow 실행하기 (tensorflow 2.5 설치)](https://gmnam.tistory.com/271)
- [uninstall mini-forge](https://github.com/conda-forge/miniforge)
- [From Zero to Transformers on Apple M1 Chip](https://medium.com/@alfarruggia/from-zero-to-transformers-on-apple-m1-chip-e5d9afa35f5)
- [Getting Started with tensorflow-metal PluggableDevice](https://developer.apple.com/metal/tensorflow-plugin/)

---

