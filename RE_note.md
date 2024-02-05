<span style="font-family:Consolas">

## 메타 문자(Meta Characters)
- 원래 그 문자가 가진 뜻이 아닌 특별한 의미를 가진 문자

### [ ] - 문자 클래스

- 괄호 내부의 문자와 매칭함을 의미한다.

  ```
  [abc]: a, b, c 중 하나의 문자와 일치함
  ```

- 하이픈을 사용하면 두 문자 사이의 범위를 의미한다.
  ```
  [a-zA-Z]: 모든 알파벳, [0-9]: 모든 숫자
  ```
- 문자 앞에 ^를 붙이면 **not**의 의미를 가진다.
  ```
  [^0-9]: 숫자가 아닌 모든 문자와 일치함
  ```
  그러나 문자 뒤에 붙으면 개별 문자로서 작용한다.
  ```
  [0-9^]: 모든 숫자 또는 ^와 일치함
  ```

- 백 슬래시 뒤에 특수 시퀀스를 나타내는 문자를 붙여 문자 클래스에 활용할 수 있다.
  
|특수 시퀀스|내용|
|---:|---:|
|\d|모든 십진 숫자와 일치함. [0-9]와 같음.|
|\D|[^0-9]와 같음.|
|\s|모든 공백 문자와 일치함. [ \t\n\r\f\v]와 같음.|
|\S|[^ \t\v\r\f\v]와 같음.|
|\w|모든 영숫자와 일치함. [a-zA-Z0-9_]와 같음.|
|\W|[a-zA-Z0-9_]와 같음.|

### .(Dot)

- 개행 문자를 제외한 모든 문자와 매칭한다.
  ```
  ca.e: cake, cave, ca e, ca1e, ...
  ```

- [.]과 같이 쓰면 문자 그대로의 의미로 쓰인다.

### *, +, ?, {m,n} - 반복 문자

- *, +: 이전 문자를 여러 번 반복하여 매칭한다. *는 0번 이상, +는 1번 이상 매칭한다.
  ```
  ca*t: ct, cat, caat, ...
  ca+t: cat, caat, caaat, ...
  ```

- ?: 이전 문자를 0번 또는 1번 매칭한다.
  ```
  home-?brew: homebrew, home-brew
  ```

- {m,n}: 이전 문자를 m번에서 n번 매칭한다.
  ```
  a/{1,3}b: a/b, a//b, a///b
  ```
  m, n을 생략할 수 있다. m을 생략하면 하한을 0으로, n을 생략하면 상한을 무한대로 설정한다.

### |
- or을 의미한다.
  ```
  abc|def: abc 또는 def
  ```

- 문자 '|'를 매칭하려면, \\|와 같이 탈출 문자를 활용하거나 [|]와 같이 문자 클래스 안에 넣어서 쓰면 된다.

### ^
- 줄의 시작 부분에서만 매칭한다.
  ```python
  >>> print(re.search('^From', 'From Here to Eternity'))  
  <re.Match object; span=(0, 4), match='From'>
  >>> print(re.search('^From', 'Reciting From Memory'))
  None
  ```

### $
- 문자열의 끝부분과 매칭한다.
  
  ```python
  >>> print(re.search('}$', '{block}'))  
  <re.Match object; span=(6, 7), match='}'>
  >>> print(re.search('}$', '{block} '))
  None
  >>> print(re.search('}$', '{block}\n'))  
  <re.Match object; span=(6, 7), match='}'>
  ```

### \A
- 문자열의 시작 부분에서만 매칭한다.
  
- ^와 거의 같지만, ^는 re.MULTILINE 옵션을 사용할 경우 줄바꿈 문자 뒤에도 매칭할 수 있다.
  
### \Z
- 문자열의 끝부분에서만 일치한다. \A와 마찬가지로 $와 같지만 $는 re.MULTILINE 옵션을 사용하면 줄바꿈 문자 앞에서도 매칭할 수 있다.

### \b
- 단어 경계를 의미한다. 단어의 시작이나 끝에서만 매칭한다.
  ```python
  >>> p = re.compile(r'\bclass\b')
  >>> print(p.search('no class at all'))
  <re.Match object; span=(3, 8),match='class'>
  >>> print(p.search('the declassified algorithm'))
  None
  >>> print(p.search('one subclass is'))
  None
  ```
  \b는 파이썬 문자열 문법에서 백스페이스를 의미하기도 하므로, 이를 사용할 경우 raw string을 이용해야 한다.
  ```python
  >>> p = re.compile('\bclass\b')
  >>> print(p.search('no class at all'))
  None
  >>> print(p.search('\b' + 'class' + '\b'))
  <re.Match object; span=(0, 7), match='\x08class\x08'>
  ```

### \B
- \b의 반대 의미이며, 현재 위치가 단어 경계에 있지 않을 때만 매칭한다.
<br/><br/><br/>

## re 모듈의 문자열 검색 도구

- 다음과 같은 패턴을 만들었다고 가정한다.
  ```python
  >>> import re
  >>> p = re.compile('[a-z]+')
  ```

### match()
- 문자열의 처음부터 일치하는지 탐색한다. 
  ```python
  >>> m = p.match("python")
  >>> m
  <re.Match object; span=(0, 6), match='python'>
  ```

  일치하지 않으면 None을 반환한다.
  ```python
  >>> p.match('3 python')
  None
  ```

### search()
- 문자열 전체를 탐색하여 일치하는 문자열을 찾는다.
  ```python
  >>> m = p.search("3 python")
  >>> m
  <re.Match object; span=(2, 8), match='python'>
  ```

### findall()
- 정규식과 일치하는 모든 문자열을 리스트로 반환한다.
  ```python
  >>> p.finditer('3 python javascript java')
  ['python', 'javascript', 'java']
  ```

### finditer()
- 정규식과 일치하는 모든 문자열을 반복 가능한 객체로 반환한다.

  ```python
  >>> iterate = p.finditer('3 python javascript java')
  >>> iterate
  <callable_iterator object at 0x000001CB23BDF940>
  >>> for r in iterate:
  ...     print(r)
  ...
  <re.Match object; span=(2, 8), match='python'>
  <re.Match object; span=(9, 19), match='javascript'>
  <re.Match object; span=(20, 24), match='java'>
  ```

## match 객체의 메서드

|메서드|설명|
|---|---|
|group()|일치하는 문자열을 반환한다.|
|start(), end()|일치하는 문자열의 시작/끝 위치를 반환한다.|
|span()|일치하는 문자열의 시작, 끝 위치에 해당하는 튜플을 반환한다.|

#### <예시>
```python
>>> m = p.search('3 python java javascript')
>>> m.group()
'python'
>>> m.start()
2
>>> m.end()
8
>>> m.span()
(2, 8)
```

## 컴파일 옵션

### re.DATALL, re.S
- 메타 문자 .의 매칭에 줄바꿈을 포함시킨다.
  ```python
  >>> p = re.compile('a.b', re.DOTALL) 
  >>> m = p.match('a\nb')
  >>> print(m)
  <re.Match object; span=(0, 3), match='a\nb'>
  ```

### re.IGNORECASE, re.I
- 대소문자 구별 없이 매칭을 수행하도록 한다.
  ```python
  >>> p = re.compile('[a-z]+', re.I)
  >>> p.match('PYTHON')
  <re.Match object; span=(0, 6), match='PYTHON'>
  ```

### re.MULTILINE, re.M
- 메타 문자 ^, $가 문자열의 시작이 아닌 줄의 시작에서 매칭하도록 한다.
  ```python
  >>> p = re.compile('^python\s\w+', re.MULTILINE)
  >>> data = '''python one
  ... life is too short
  ... python two
  ... you need python
  ... python three'''
  >>> p.findall(data)
  ['python one', 'python two', 'python three']
  ```

### re.VERBOSE, re.X
- 문자열에 사용된 화이트스페이스가 컴파일 과정에서 제거된다.(단, [ ] 안에 있는 화이트스페이스는 제외)
  ```python
  # Nooooooo
  charref = re.compile(r'&[#](0[0-7]+|[0-9]+|x[0-9a-fA-F]+);')

  # Good!
  charref = re.compile(r"""
   &[#]                # Start of a numeric entity reference
   (
       0[0-7]+         # Octal form
     | [0-9]+          # Decimal form
     | x[0-9a-fA-F]+   # Hexadecimal form
   )
   ;                   # Trailing semicolon
  """, re.VERBOSE)
  ```

## 역슬래시 문제와 raw string
어떤 파일 안에 있는 '\string' 문자열을 찾기 위한 정규식을 작성한다고 하자. 그런데, \s는 공백 문자로 해석되어 원래 의도대로 매칭되지 않는다.
```
\string = [ \t\n\r\f\v]tring
```
이 경우, 역슬래시를 두 번 입력해야 한다.
```
\\string
```
그러나, 컴파일 과정에서 문제가 또 생긴다. 파이썬 리터럴 규칙에 따라 \\가 \로 바뀌어 모듈에 전달된다. 따라서 모듈에 \\를 전달하려면 역슬래시를 네 개나 써야 한다.
```python
p = re.compile('\\\\string')
```
이런 중복을 방지하려면, 파이썬의 리터럴 규칙을 무시하는 raw string을 사용하면 된다.
```python
p = re.compile(r'\\string')
```
<br/>

## 그룹

### ( ) - 그루핑
- 괄호 안의 문자들을 그룹으로 만든다.
  ```python
  >>> p = re.compile('(ab)*')
  >>> print(p.match('ababababab').span())
  (0, 10)
  ```

- 그룹은 중첩하여 사용할 수 있다. group(), start(), end(), span() 등에 인자를 전달하여 서브 그룹을 꺼낼 수 있다. (default: 0)
  ```python
  >>> p = re.compile('(a(b)c)d')
  >>> m = p.match('abcd')
  >>> m.group(0)
  'abcd'
  >>> m.group(1)
  'abc'
  >>> m.group(2)
  'b'
  >>> m.group(2, 1, 2)
  ('b', 'abc', 'b')
  ```

- groups() 메서드를 사용하면 모든 서브 그룹에 대한 문자열을 포함하여 반환한다.(1부터)
  ```python
  >>> m.groups()
  ('abc', 'b')
  ```

- 역슬래시 뒤에 숫자를 붙여 이전 그룹을 재참조할 수 있다.
  ```python
  >>> p = re.compile(r'\b(\w+)\s+\1\b') # \1은 첫 번째 그룹을 재참조한다는 의미
  >>> p.search('Paris in the the spring').group()
  'the the'
  ```

- 그룹에 이름을 지정할 수도 있다. (?P<group_name>)과 같이 쓰면 된다. group() 안에 참조할 그룹의 이름을 인자로 넣어주면 참조가 가능하다.
  ```python
  >>> p = re.compile(r"(?P<name>\w+)\s+((\d+)[-]\d+[-]\d+)")
  >>> m = p.search("park 010-1234-1234")
  >>> print(m.group("name"))
  park
  ```
  그룹 안에서 재참조할 때에는 (?P=group_name)과 같이 쓴다.
  ```python
  >>> p = re.compile(r'(?P<word>\b\w+)\s+(?P=word)')
  >>> p.search('Paris in the the spring').group()
  'the the'
  ```
<br/>

## 전방 탐색

- 해당 조건을 만족하는 문자열에 대해서만 검색한다. 조건을 만족해도 문자열이 소비되지 않는다.

### 긍정형 전방 탐색
- (?=expression)과 같이 쓰이며, expression에 해당하는 부분과 매칭되어야 한다.

  ```python
  >>> p = re.compile(".+(?=:)")
  >>> m = p.search("http://google.com")
  >>> print(m.group())
  http
  ```

### 부정형 전방 탐색
- (?!expression)과 같이 쓰이며, expression에 해당하는 부분과 매칭되지 않아야 한다.
  
  ```python
  >>> p = re.compile('.+[.](?!bat$|exe$).+$', re.MULTILINE) 
  >>> m = p.findall(
  ... '''example1.bat
  ... example2.exe
  ... example3.txt
  ... example4.docx'''
  ... )
  >>> m
  ['example3.txt', 'example4.docx']
  ```

## sub() - 문자열 바꾸기
- sub('바꿀 문자열', '대상 문자열', count=바꾸는 횟수)

  ```python
  >>> p = re.compile('(blue|white|red)')  
  >>> p.sub('colour', 'blue, white, red', count=2)
  'colour, colour, red'
  ```

### 참조 구문 사용하기
- sub 메서드에서 참조 구문을 사용하려면 \g<group_name>과 같이 사용한다.
  ```python
  >>> p = re.compile(r'''(?P<name>
  ...     \w+)                            # park
  ...     \s+                             # whitespace
  ...     (?P<phone>
  ...      \d+[-]                         # 010-
  ...      \d+[-]                         # 1234-
  ...      \d+                            # 1234
  ...     )
  ...     ''', re.VERBOSE)
  >>> p.sub('\g<phone> \g<name>', 'park 010-1234-1234')
  '010-1234-1234 park'
  ```

### 매개 변수로 함수 지정
- sub 메서드의 첫 번째 인자로 함수를 지정하면, 그 함수의 첫 번째 매개변수는 대상 문자열을 매치한 match 객체가 된다.
  ```python
  >>> def hexrepl(match):
  ...     value = int(match.group())
  ...     return hex(value) # hex: 16진법 변환
  ...
  >>> p = re.compile(r'\d+')
  >>> p.sub(hexrepl, '2024-10-26')        
  '0x7e8-0xa-0x1a'
  ```
<br/>

## greedy와 non-greedy
- 반복 메타 문자인 *, +, ?, {m, n}은 매치할 수 있는 최대한의 문자열을 반복시킨다.

- non-greedy 문자인 ?를 사용하여 최소한의 문자열로 반복시킬 수 있다.
  
  ```python
  >>> s = '<html><head><title>Title</title>' 
  
  >>> re.findall('<.*>', s)     
  ['<html><head><title>Title</title>']
  
  >>> re.findall('<.*?>', s) 
  ['<html>', '<head>', '<title>', '</title>']
  ```