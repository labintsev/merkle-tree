# Merkle Tree

В проекте представлены основные классы данных, которые используются в блокчейне. 
Необходимо написать реализацию методов и проверить правильность с помощью тестов.  
Для запуска тестов необходимо установить [nodejs](https://nodejs.org/) и необходимые библиотеки.  

## Установка 

Клонируйте репозиторий, перейдите в каталог проекта и установите зависимости.  
```bash 
git clone https://github.com/labintsev/merkle-tree
cd merkle-tree
npm install
```

Запуск всех тестов 
```bash
npx mocha test
```

## 1. Blockhain primitive

Блокчейн - это структура данных, состоящая из нескольких блоков. 
В этом упражнении класс `Block` представляет собой простую обертку над некоторыми данными. 
Для упрощения мы считаем что `data` - это просто некоторая строка.  

Кроме данных, блок содержит поле `previousHash`, которое хранит в себе хэш предыдущего блока в блокчейне.
Хэш блока вычисляется от конкатенации двух полей `data + previousHash`. 
Итерактивное демо https://blockchaindemo.io/   

Каждый новый блок добавляется в блокчейн через вызов метода `addBlock(block)`. 
Перед добавлением в цепочку в поле `block.previousHash` необходимо записать хэш предыдущего блока. 
Реализуйте эту логику.  


Когда к сети подключается новый участник, он должен проверить правильность построения всей цепочки. 
Метод `isValid()` возвращает `true`, если для каждого блока в цепочке в поле `previousHash` содержится хэш предыдущего блока. 
При любом несовпадении возвращается `false`. 
Реализуйте эту логику.    

Запуск теста:  

```sh
npx mocha test/Blockchain.js
``` 

## 2. Transaction output

Объект `TXO` - transaction output, содержит информацию об адресе владельца и количестве монет. 
Реализуйте конструктор транзакции с полями `owner, amount, spent, hash`. 

Метод `spend()` означает проведение транзакции и может быть вызван только один раз. 
Реализуйте метод `spend()`, который будет устанавливать поле `spent` в значение `true`. 
При попытке провести транзакцию дважды должна бросаться ошибка `Error('Already spended!')`.  

Запуск теста:  

```sh
npx mocha test/TXO.js
``` 

## 3. Binary search tree 

Транзакции хранятся в блоке в специальной структуре данных - модифицированном дереве Меркла. 
В отличии от линейных структур данных типа массива или связного списка, деревья позволяют осуществлять эффективный поиск за логарифмическое время.  

Мы пойдем от простого к сложному и для начала вспомним, как реализуются основные методы классического бинарного дерева поиска. 
Класс `Node` является узлом бинарного дерева, помимо полезных данных от содержит ссылки на левого и правого потомка. 
Здесь важно не запутаться в синонимах, под словом `Node` мы также можем иметь ввиду узел вычислительной сети. 
Практически всегда можно понять о какой ноде идет речь из контекста повествования.  

Класс `Tree` - классическое бинарное дерево поиска.  

В файле `Tree.js` реализуйте метод `addNode(node)` для добавления узла. 

Реализуйте метод `hasNode(data)` для проверки, есть ли в дереве узел с таким содержимым.  

Запуск теста:  

```sh
npx mocha test/Tree.js
``` 


## 4. Merkle tree

Транзакции в блоке хранятся в замечательной структуре данных - дереве Меркла. 
Главное преимущество дерева Меркла состоит в том, что для реализации метода `hasNode(data)` нам не нужно хранить сами данные. 
Для проверки того, есть ли в данном дереве такой узел, используется лишь хеш данных этого узла. 
Нам даже не нужно знать хеши всех других транзакций. 

Дерево Меркла хранит данные только в листовых узлах. 
Промежуточные узлы хранят только конкатенированные значения хешей левого и правого потомков.  

```sh
      ABCDEFGH <-- Merkle Root  
       /    \  
    ABCD     EFGH  
    / \      / \  
   AB  CD   EF  GH  
  / \  / \  / \ / \  
  A B  C D  E F G H    
```
В этом примере каждая буква представляет собой уникальное значение хеша. 
Комбинация АВ означает, что мы берем хеш от конкатенации этих букв.  

В учебных целях мы используем очень простую функцию конкатенации: 

```js
function concatHashes(a, b) {
    return `Hash(${a} + ${b})`;
} 
``` 

Реализуйте метод `getRoot()` класса `MerkleTree`, который построит все дерево хешей и вернет хеш самого верхнего уровня. 
Можно использовать итеративный подход, но рекомендуется рекурсивный. 
Количество слоев можно вычислить заранее, или использовать условие остановки для рекурсии. 
Обратите внимание на особый случай - когда количество листьев будет нечетным.  

Цепочка для доказательства принадлежности листа данному дереву носит название `proof`. 
Реализуйте метод `getProof(index)`, который будет возвращать цепочку из узлов, от корня `root` до листа с индексом `index`.  


Реализуйте функцию `verifyProof(proof, node, root)` для проверки того, входит ли узел в данное дерево. 
`proof` - массив из объектов, имющих поля `data` и `left` 
`node` - проверяемый узел
`root` - корень дерева. 



Запуск теста:  

```sh
npx mocha test/MerkleTree.js
```  

