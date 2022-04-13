# Dictionary
기본 딕셔너리의 모습<br>
```
{Key1:Value1, Key2:Value2, Key3:Value3, ...}
```
Value에 리스트도 넣을 수 있다.<br>
```python
a = { 'a': [1,2,3]}
```
### 딕셔너리 쌍 추가하기
```python
>>> a = {1: 'a'}
>>> a[2] = 'b'
>>> a
{1: 'a', 2: 'b'}
```
### 딕셔너리 요소 삭제하기
```python
>>> del a[1]
>>> a
{2: 'b', 'name': 'pey', 3: [1, 2, 3]}
```
### 딕셔너리에서 Key 사용해 Value 얻기
```python
>>> grade = {'pey': 10, 'julliet': 99}
>>> grade['pey']
10
>>> grade['julliet']
99
```
리스트나 튜플, 문자열은 요솟값을 얻고자 할 때 인덱싱이나 슬라이싱 기법 중 하나를 사용했다. <br>
하지만 딕셔너리는 단 한 가지 방법뿐이다. 바로 Key를 사용해서 Value를 구하는 방법이다. 
<br>위 예에서 'pey'라는 Key의 Value를 얻기 위해 grade['pey']를 사용한 것처럼 어떤 Key의 Value를 얻기 위해서는 딕셔너리변수이름[Key]를 사용한다.
### 딕셔너리 만들 때 주의할 사항
먼저 딕셔너리에서 Key는 고유한 값이므로 중복되는 Key 값을 설정해 놓으면 하나를 제외한 나머지 것들이 모두 무시된다는 점을 주의해야 한다. <br>
다음 예에서 볼 수 있듯이 동일한 Key가 2개 존재할 경우 1:'a' 쌍이 무시된다.
```python
>>> a = {1:'a', 1:'b'}
>>> a
{1: 'b'}
```
<br>
또 한 가지 주의해야 할 사항은 Key에 리스트는 쓸 수 없다는 것이다. 하지만 튜플은 Key로 쓸 수 있다. <br>
딕셔너리의 Key로 쓸 수 있느냐 없느냐는 Key가 변하는 값인지 변하지 않는 값인지에 달려 있다. 리스트는 그 값이 변할 수 있기 때문에 Key로 쓸 수 없다. <br>
단 Value에는 변하는 값이든 변하지 않는 값이든 상관없이 아무 값이나 넣을 수 있다.<br>

## 딕셔너리 관련 함수들
### Key 리스트 만들기(keys)
```python
>>> a = {'name': 'pey', 'phone': '0119993323', 'birth': '1118'}
>>> a.keys()
dict_keys(['name', 'phone', 'birth'])
```
3.0 이후 버전에서 반환 값으로 리스트가 필요한 경우에는 list(a.keys())를 사용<br>
dict_keys 객체는 다음과 같이 사용할 수 있다. 리스트를 사용하는 것과 차이가 없지만, **리스트 고유의 append, insert, pop, remove, sort 함수는 수행할 수 없다.** <br>

```python
>>> for k in a.keys():
...    print(k)
...
name
phone
birth
```

### Value 리스트 만들기(values)
```python
>>> a.values()
dict_values(['pey', '0119993323', '1118'])
```
### Key, Value 쌍 얻기(items)
```python
>>> a.items()
dict_items([('name', 'pey'), ('phone', '0119993323'), ('birth', '1118')])
```
items 함수는 Key와 Value의 쌍을 튜플로 묶은 값을 dict_items 객체로 돌려준다. <br>
dict_values 객체와 dict_items 객체 역시 dict_keys 객체와 마찬가지로 리스트를 사용하는 것과 동일하게 사용할 수 있다.<br>
### Key: Value 쌍 모두 지우기(clear)
```python
>>> a.clear()
>>> a
{}
```
### Key로 Value얻기(get)
```python
>>> a = {'name':'pey', 'phone':'0119993323', 'birth': '1118'}
>>> a.get('name')
'pey'
>>> a.get('phone')
'0119993323'
```
get(x) 함수는 x라는 Key에 대응되는 Value를 돌려준다. <br>
앞에서 살펴보았듯이 a.get('name')은 a['name']을 사용했을 때와 동일한 결괏값을 돌려받는다.<br>
다만 다음 예제에서 볼 수 있듯이 a['nokey']처럼 존재하지 않는 키(nokey)로 값을 가져오려고 할 경우 a['nokey']는 Key 오류를 발생시키고 a.get('nokey')는 None을 돌려준다는 차이가 있다.<br>
딕셔너리 안에 찾으려는 Key 값이 없을 경우 미리 정해 둔 디폴트 값을 대신 가져오게 하고 싶을 때에는 get(x, '디폴트 값')을 사용하면 편리하다.<br>
```python
>>> a.get('foo', 'bar')
'bar'
```
### 해당 Key가 딕셔너리 안에 있는지 조사하기(in)
```python
>>> a = {'name':'pey', 'phone':'0119993323', 'birth': '1118'}
>>> 'name' in a
True
>>> 'email' in a
False
```
