---
title: Сортировка слиянием
weight: 4
draft: true
---

## Сортировки за $O(n \log n)$

Сортировка очень часто применяется как часть решения олимпиадных задач. В таких случаях обычно не пишут её заново, а используют встроенную.

Пример на Python:

```python
a = [1, 5, 10, 5, -4]
a.sort()
```

Пример на C++:

```cpp
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

int main() {
    vector<int> v = {1, 5, 10, 5, -4};
    sort(v.begin(), v.end());
}
```

В разных языках она может быть реализована по-разному, но везде она работает за $O(n \log n)$, и, обычно, неплохо оптимизирована. Далее мы опишем два подхода к её реализации.

## Сортировка слиянием

> Найти количество пар элементов $a$ и $b$ в отсортированном массиве, такие что $b - a > K$.

Наивное решение: бинарный поиск. Будем считать, что массив уже отсортирован. Для каждого элемента $a$ найдем первый справа элемент $b$, который входит в ответ в паре с $a$. Нетрудно заметить, что все элементы, большие $b$, также входят в ответ. Итоговая асимптотика $O(n\log n)$.

А можно ли быстрее?

Да, давайте перебирать **два указателя** — два индекса $first$ и $second$. Будем перебирать $first$ просто слева направо и поддерживать для каждого $first$ первый элемент справа от него, такой что $a[second] - a[first] > K$ как $second$. Тогда в пару к $a=a[first]$ подходят ровно $n-second$ элементов массив начиная с $second$.

```cpp
int second = 0, ans = 0;
for (int first = 0; first < n; ++first) {
    while (second != n && a[second] - a[first] <= r) {
        second++;
    }

    ans += n - second;
}
```

За сколько же работает это решение? С виду может показаться, что за $O(n^2)$, но давайте посмотрим сколько раз меняется значение переменной $second$. Так как оно изначально равняется нулю, только увеличивается и не может превысить $n$, то суммарно операций мы сделаем $O(n)$.

Это называется метод двух указателей — так как мы двигаем два указателя first и second одновременно слева направо по каким-то правилам. Обычно его используют на одном отсортированном массиве.

Давайте разберем еще примеры.

## Слияние

Еще пример двух указателей на нескольких массивах.

Пусть у нас есть два отсортированных по неубыванию массива размера $n$ и $m$. Хотим получить отсортированный массив размера $n + m$ из исходных.

Пусть первый указатель будет указывать на начало первого массива, а второй, соответственно, на начало второго. Из двух текущих элементов, на которые указывают указатели, выберем наименьший и положим на соответствующую позицию в новом массиве, после чего сдвинем указатель. Продолжим этот процесс пока в обоих массивах не закончатся элементы. Тогда код будет выглядеть следующим образом:

```cpp
int a[n + 1], b[m + 1], res[n + m];

a[n] = INF; // Создаем в конце массива фиктивный элемент, который заведомо больше остальных
b[m] = INF; // Чтобы избежать лишних случаев

for (int i = 0; i < n; ++i) {
    cin >> a[i];
}

for (int j = 0; j < m; ++j) {
    cin >> a[j];
}

int i = 0, j = 0;
for (int k = 0; k < n + m; ++k) {
    if (a[i] < b[j]) {
        res[k] = a[i];
        i++;
    } else {
        res[k] = b[j];
        j++;
    }
}
```

Итоговая асимптотика: $O(n + m)$.

## Сортировка слиянием

Давайте подробно опишем как использовать операцию слияния для сортировки за $O(n\log n)$.

Пусть у нас есть какой-то массив.

```cpp
int a[8] = {7, 2, 5, 6, 1, 3, 4, 8};
```

Сделаем такое предположение. Пусть мы уже умеем как-то сортировать массив размера $n$. Тогда научимся сортировать массив размера $2n$.
Давайте разобьем наш массив на две половины, отсортируем каждую из них, а после это сделаем слияние двух массивов, которое мы научились делать за $O(n)$ в данных условиях. Также заметим, что массив размера $1$ уже отсортирован, тогда мы можем делать это процедуру рекурсивно. Тогда для данного массива $a$ это будет выглядеть следующим образом:

```cpp
// (7  2  5  6  1  3  4  8)
// (7  2  5  6)(1  3  4  8)
// (7  2)(5  6)(1  3)(4  8)
// (2  7)(5  6)(1  3)(4  8)
// (2  5  6  7)(1  3  4  8)
// (1  2  3  4  5  6  7  8)

#include  // Воспользуемся встроенной функцией merge

void merge_sort(vector &v, int l, int r) { // v - вектор, который хотим отсортировать
    if (r - l == 1) {                            // l и r - полуинтервал, который хотим отсортировать
        return;
    }

    int mid = (l + r) / 2;
    merge_sort(v, l, mid);
    merge_sort(v, mid, r);
    vector temp(r - l); // временный вектор
    merge(v.begin() + l, v.begin() + mid, v.begin() + mid, v.begin() + r, c.begin());
    for (int i = 0; i < r - l; ++i) {
        v[i + l] = temp[i];
    }
    return;
}
```

Так сколько же работает это решение?

Пускай $T(n)$ — время сортировки массива длины $n$, тогда для сортировки слиянием справедливо $T(n)=2T(n/2)+O(n)$
 $O(n)$ — время, необходимое на то, чтобы слить два массива длины n. Распишем это соотношение:

$T(n)=2T(n/2)+O(n)=4T(n/4)+2O(n)=\ldots=T(1)+\log(n)O(n)=O(n\log(n)).$

## Количество инверсий

Пусть у нас есть некоторая перестановка $a$. Инверсией называется пара индексов $i$ и $j$ такая, что $i < j$ и $a[i] > a[j]$.

> Найти количество инверсий в данной перестановке.

Очевидно, что эта задача легко решается обычным перебором двух индексов за $O(n^2)$:

```cpp
int a[n], ans = 0;

for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
        if (a[i] > a[j]) {
            ans++;
        }
    }
}

cout << ans << endl;
```

Внезапно эту задачу можно решить используя сортировку слиянием, слегка модифицируя её. Оставим ту же идею. Пусть мы умеем находить количество инверсий в массиве размера $n$, научимся находить количество инверсий в массиве размера $2n$.

Заметим, что мы уже знаем количество инверсий в левой половине и в правой половине массива. Осталось лишь посчитать число инверсий, где одно число лежит в левой половине, а второе в правой половине. Как же их посчитать?

Давайте подробнее рассмотрим операцию merge левой и правой половины (которую мы ранее заменили на вызов встроенной функции merge). Первый указатель указывает на элемент левой половины, второй указатель указывает на элемент второй половины, мы смотрим на минимум из них и этот указатель вдигаем вправо.

Рассмотрим число $A$ в левой половине. В скольки инверсиях между половинами оно участвует? В стольки, сколько чисел в правой половине меньше, чем оно. Знаем ли мы это количество? Да! Ровно в тот момент, когда мы число $A$ вносим в слитый массив, второй указатель указывает на первое число в правой половине, которое больше чем $A$.

Значит в тот момент, когда мы добавляем число $A$ из левой половины, к ответу достаточно прибавить индекс второго указателя (минус начало правой половины). Так мы учтем все инверсии между половинами.

---

Допустим, нам надо отсортировать массив из $N$ чисел. Заметим, что если
в нем только одно число($N = 1$), то массив уже отсортирован. Иначе
предположим, что массивы размеров <em>меньше $N$</em> мы уже умеем
сортировать. Тогда сделаем следующее: разделим массив на две примерно
равные части (просто делим пополам), отдельно отсортируем левую,
отдельно отсортируем правую часть. Нам останется только
объединить эти два массива и получить результат.

<b>Пример</b>:

Пусть дан массив $a = \[7, 0, 1, 3, 5, 2, 6, 4\]$. В таблице ниже
показано, в каком порядке будут происходить деления и как потом
ответ будет собираться из отсортированных кусочков.

|    Разделяем    |
| :-------------: |
| 7 0 1 3 5 2 6 4 |
|     7 0 1 3     |
|       7 0       |
|        7        |
|     Сливаем     |
|       0 7       |
|     0 1 3 7     |
| 0 1 2 3 4 5 6 7 |

### Функция Merge

Научимся <em>сливать</em> (объединять) два отсортированных массива. Для
этого воспользуемся техникой <em>двух указателей</em>. Пусть указатель
$p_1$ стоит на некотором элементе первого массива, указатель $p_2$ на
элементе второго массива. Будем поддерживать условие, что сейчас в
отсортированный массив надо добавить либо элемент $p_1$, либо
$p_2$. Какой именно - можно узнать, сравнив между собой эти два
элемента. Если $a\[p_1\] \\le b\[p_2\]$, то надо сложить в
ответ $a\[p_1\]$ и сдвинуть $p_1$ на единицу вправо. Иначе делаем
то же самое, но уже с $p_2$.

Количество смещений двух указателей не превосходит суммы длин двух
массивов, поэтому такое слияние выполнится за $O(n + m)$, где $n,
m$ - длины двух массивов.

### Асимптотика

Вычислим итоговое время работы MergeSort.

<b>Первое доказательство - наглядное</b>

Вспомним наш пример. Заметим, что каждый уровень состоит из одинакового
количества элементов, на каждом уровне происходит серия операций merge,
но суммарно на каждом уровне все они выполнятся за $O(n)$, так как время
на merge линейно зависит от количества чисел в массивах. Теперь оценим
количество уровней. На каждом размер сортируемого блока уменьшается в
два раза, быть меньше единицы этот блок не может(иногда они вырождаются
в пустые блоки, если длины массива - не степень двойки), поэтому уровней
будет столько, сколько раз $n$ можно поделить на $2$. Это $\\log n$.
Тогда весь алгорритм работает за $O(n\\log n)$.

<b>Второе доказательство - формальности</b>

Пусть $T(n)$ - время, за которое мы умеем сортировать массив длины $n$.
Предположим, что $T(n) \< Cn\\log n$ для некоторой константы $C$.
Теперь посмотрим, из чего складывается время сортировки:

$T(n) = 2T(n/2) + An$, где $A$ - какая-то константа, а $An$ - время на
merge.

$T(n) \< 2C\\frac{n}{2}\\log \\frac{n}{2} + An =Cn\\log \\frac{n}{2} +
An = n(C\\log n - C + A) \< Cn\\log n$

если $C \> A$. Такие константы мы можем подобрать, поэтому доказана
верхняя оценка $n\\log n$ на время работы сортировки слиянием.