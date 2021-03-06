# Tensorflow Tips 

## 1. [Eager Execution Mode](https://www.tensorflow.org/guide/eager) [[한글]](https://github.com/tgjeon/TF-Eager-Execution-Guide-KR/blob/master/guide.md)



```
import tensorflow as tf

tf.executing_eagerly() # TF 2.x 
#tf.enable_eager_execution() #TF 1.x

tf.compat.v1.disable_eager_execution()




model = multi_gpu_model(model, 2, cpu_relocation=True)
tf.test.is_gpu_available()
```




```python 
is_training_pl = tf.placeholder(shape=(),dtype=tf.bool, name="b")
is_training_pl = tf.Variable(tf.zeros(shape=(),dtype=tf.bool), name="b")
tf.compat.v1.Variable(name, shape, dtype=dtype)


tf.train.exponential_decay -> tf.compat.v1.train.exponential_decay
tf.get_variable -> tf.Variable
initializer=tf.contrib.layers.xavier_initializer())  -> initializer = tf.initializers.GlorotUniform()
tf.get_variable -> tf.Variable -> tf.compat.v1.Variable(name, shape, initializer=initializer, dtype=dtype)
tf.contrib.layers.batch_norm() --> tf.compat.v1.layers.BatchNormalization
#https://www.tensorflow.org/api_docs/python/tf/layers/BatchNormalization?hl=ko

```

> https://www.tensorflow.org/beta/guide/migration_guide?hl=ko#%EB%B3%80%ED%99%98_%EC%A0%84


- [Eager 모드 실행을 위해 경할 코드 8가지](https://medium.com/coinmonks/8-things-to-do-differently-in-tensorflows-eager-execution-mode-47cf429aa3ad) : Placeholders, Variables, 

---



## 2. `ft.py_func`


Python 코드를 텐서플로우에서 실행 하는 방법 [[참고]](https://tensorflowkorea.gitbooks.io/tensorflow-kr/content/g3doc/api_docs/python/script_ops.html)





## 3. Eager - Graph 


```python 

# Eager 
def measurement(gpu=False):
    if gpu:
        device = "/gpu:0"
    else:
        device = "/cpu:0" 
        
    with tf.device(device):
        with tf.GradientTape() as tape:
            y_pre = model(x)
            loss_value = loss(y, y_pre)
        grads = tape.gradient(loss_value, model.variables)
        optimizer.apply_gradients(zip(grads, model.variables))



# Graph 
@tf.contrib.eager.defun
def graph_measurement(gpu=False):
    if gpu:
        device = "/gpu:0"
    else:
        device = "/cpu:0" 
        
    with tf.device(device):
        with tf.GradientTape() as tape:
            y_pre = model(x)
            loss_value = loss(y, y_pre)
        grads = tape.gradient(loss_value, model.variables)
        optimizer.apply_gradients(zip(grads, model.variables))
```







---
## 하이레벨 API

> 출처 : [텐서플로우 하이레벨 API](http://bcho.tistory.com/1195), [Estimator를 이용한 모델 정의 방법](http://bcho.tistory.com/1196)

- tf.contrib: 공식 텐서플로우의 하이레벨 API

- Keras : 공식 하이레벨 API로 로 편입

## 1. Estimator

![](http://cfile30.uf.tistory.com/image/9910C53359AF8CA334DC82)

Estimator: 학습(Training), 테스트(Evaluation), 예측(Prediction)을 한후, 학습이 완료된 모델을 저장(Export)하여 배포 단계를 추상화 한것 

Estimator는
- 직접 개발자가 모델을 직접 구현하여 Estimator를 개발할 수 도 있고 (Custom Estimator)
- 또는 이미 텐서플로우 tf.contrib.learn에 에 미리 모델들이 구현되어 있다.

### 1.1 Estimator 예제

https://github.com/bwcho75/tensorflowML/blob/master/HighLevel%20API%201.%20Linear%20Regression%20Estimator.ipynb



- [새로운 텐서플로 개발 트랜드 Estimator](http://chanacademy.tistory.com/33)


---



# MNIST Offline 

```python 
#https://s3.amazonaws.com/img-datasets/mnist.pkl.gz
import gzip
import sys
import pickle as cPickle
f = gzip.open('mnist.pkl.gz', 'rb')
if sys.version_info < (3,):
    data = cPickle.load(f)
else:
    data = cPickle.load(f, encoding='bytes')
f.close()

(x_train, _), (x_test, _) = data
```

---


## Parse Jupyter 에서 사용 

`import sys; sys.argv=['']; del sys`
