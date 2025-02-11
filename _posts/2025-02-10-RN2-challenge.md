---
layout: post
title:  "React Native - 기초2 Challenge"
date:   2025-02-10 23:00 +09:00
categories: FE
---
> 해당 내용은 노마드 코더의 React Native 강의를 보고 정리한 내용입니다.

## 목표

### 1. Work / Travel 상태 저장해놓고 앱 처음 켰을 때 그 상태에 있게 하기
```javascript
const STORAGE_WORKING_KEY = "@working";

useEffect(() => {
    loadState();
    loadToDos();
  }, []);

const travel = () => {
    setWorking(false);
    saveState(false);
  }
  const work = () => {
    setWorking(true);
    saveState(true);
  }

const saveState = async (toSave) => {
    await AsyncStorage.setItem(STORAGE_WORKING_KEY, JSON.stringify(toSave));
  };

const loadState = async () => {
    const a = await AsyncStorage.getItem(STORAGE_WORKING_KEY);
    a !== null ? setWorking(JSON.parse(a)) : null;
  };

const doneToDo = async (key) => {
    const newToDos = {
      ...toDos,
      [key]: { ...toDos[key], completed: !toDos[key].completed },
    };
    setToDos(newToDos);
    await saveToDos(newToDos);
  };
```
- work 상태일 때 `saveState(true);`을 호출하여 새로운 상태를 AsyncStorage에 저장


### 2. list에 todo 상태 볼 수 있게 하기
- todo 아이콘 두기
- completed 상태 넣어서 bool 값
- 함수 만들고 키 값으로 찾아서 completed 수정 -> newState

```javascript
const doneToDo = async (key) => {
    const newToDos = {
      ...toDos,
      [key]: { ...toDos[key], completed: !toDos[key].completed },
    };
    setToDos(newToDos);
    await saveToDos(newToDos);
  };
```
- key의 completed 값을 변경

```javascript
<TouchableOpacity onPress={() => doneToDo(key)}>
    <Fontisto 
        name={toDos[key].completed ? "checkbox-active" : "checkbox-passive"}
        size={18}
        color={theme.grey} 
    />
</TouchableOpacity>
<Text style={[styles.toDoText, toDos[key].completed ? styles.completedText : null]}>{toDos[key].text}</Text>
```
- `<Text style={[styles.toDoText, toDos[key].completed ? styles.completedText : null]}>{toDos[key].text}</Text>`
    - style 안을 [] 배열로 감싸면 styles.toDoText는 항상 적용되고 completedText는 completed 상태일 때만 적용됨


### 3. text 수정 가능하게 하기
- 수정 아이콘 두기
- 수정 상태에서는 text input 상태


```javascript
const [editingKey, setEditingKey] = useState(null);
const [editText, setEditText] = useState("");
```
- editingKey: 현재 수정 중인 To-Do의 key를 저장
- editText: 사용자가 입력한 수정된 텍스트를 저장

```javascript
const startEditing = (key) => {
    setEditingKey(key); // 수정할 To-Do의 key 저장
    setEditText(toDos[key].text); // 해당 To-Do의 기존 텍스트를 가져와 editText에 저장
  };

const finishEditing = async (key) => {
    const newToDos = { 
      ...toDos, 
      [key]: { ...toDos[key], text: editText } // 텍스트 업데이트
    };
    setToDos(newToDos); // 상태 업데이트
    await saveToDos(newToDos); // AsyncStorage에 저장
    setEditingKey(null); // 수정 모드 종료
  };
```
```javascript
{editingKey === key ? (
    <TextInput 
        style={styles.toDoEditInput}
        value={editText}
        returnKeyType="done"
        onChangeText={setEditText}
        onSubmitEditing={() => finishEditing(key)}
    />
    ) : (
    <Text style={[styles.toDoText, toDos[key].completed ? styles.completedText : null]}>{toDos[key].text}</Text>
)}
```

- <img src="https://github.com/user-attachments/assets/f8fed4b3-fa6c-49ce-ad04-c747c37b69f8" width="20%" height="20%">

