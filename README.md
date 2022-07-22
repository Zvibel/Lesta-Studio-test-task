1. На языке Python реализовать алгоритм (функцию) определения четности целого числа, который будет аналогичен нижеприведенному по функциональности, но отличен по своей сути. Объяснить плюсы и минусы обеих реализаций.

Python example:

def isEven(value):return value%2==0

Решение:

def isEven2(value):
    return value & 1 == 0
    
Плюсы обеих реализаций - простота понимания.
Плюс использования оператора & (если замеры произвести) скорость выполнения выше, хоть и ненамного.
Собственно минус 1 реализации в том, что он медленнее.

2. На языке Python (2.7) реализовать минимум по 2 класса реализовывающих циклический буфер FIFO. Объяснить плюсы и минусы каждой реализации.

Решение:

Первый класс представляет собой простую реализацию массива FIFO без возможности перезаписывать существующие данные. Попытка записать данные в переполненный буфер или 
прочитать данные из пустого буфера вызовет ошибку.

class RingBuffer:
    def __init__(self, capacity):
        self.capacity = capacity
        self.data = []
    
    def write(self, data):
        if len(self.data) < self.capacity:
            self.data.append(data)
        else:
            raise Exception("Trying to write in full ring buffer")
        
    def read(self):
        try:
            return self.data.pop(0)
        except IndexError:
            raise Exception("Trying to read from empty ring buffer")
            
Второй класс - это кольцевой буфер с возможностью перезаписи существующих данных. Здесь, также как и в первом классе попытка прочитать данные из пустого буфера вызовет 
ошибку.
            
class RingBuffer:
    def __init__(self, capacity):
        self.capacity = capacity
        self.data = [None for i in range(capacity)] # junk values
        self.write_to = 0
        self.read_from = 0
    
    def _inc(self, inx):
        return (inx + 1) % self.capacity
    
    def write(self, data):
        if self.write_to == self.read_from and self.data[self.write_to] is not None: # value is overwritten
            self.read_from = self._inc(self.read_from)
        self.data[self.write_to] = data
        self.write_to = self._inc(self.write_to)
        #print(f"DEBUG: write_to {self.write_to}")
        
    def read(self):
        data = self.data[self.read_from]
        if data is None:
            raise Exception("Trying to read from an empty ring buffer")
        self.data[self.read_from] = None
        self.read_from = self._inc(self.read_from)
        #print(f"DEBUG: read_from {self.read_from}")
        return data
    
    def __str__(self):
        return self.data.__str__()

3. На языке Python реализовать функцию, которая быстрее всего (по процессорным тикам) отсортирует данный ей массив чисел. Массив может быть любого размера со случайным порядком чисел (в том числе и отсортированным). Объяснить почему вы считаете, что функция соответствует заданным критериям.

Решение:

from random import randint
import time


def quicksort(array):
    if len(array) < 2:
        return array

    low, same, high = [], [], []

    pivot = array[randint(0, len(array) - 1)]

    for item in array:
        if item < pivot:
            low.append(item)
        elif item == pivot:
            same.append(item)
        elif item > pivot:
            high.append(item)

    return quicksort(low) + same + quicksort(high)


ARRAY_LENGTH = 100000

if __name__ == "__main__":
    array = []
    for i in range(ARRAY_LENGTH):
        array.append(randint(0, 1000))

    start = time.process_time()
    quicksort(array)
    end = time.process_time()
    print(f"quicksort: {end - start}s")

    start = time.process_time()
    sorted(array)
    end = time.process_time()
    print(f"sorted: {end - start}s")
    
В Python есть возможность сортировки с помощью sorted().Основная идея данного решения в том, что все необходимые функции уже встроены или представлены в стандартных 
библиотеках. Как правило, эти функции протестированы и оптимизированы разработчиками и будут работать стабильнее и быстрее, чем данная (собственная) реализация. 

Для этого я сравниваю встроенную в Python функцию сортировки и собственную реализацию на массиве из 100000 чисел.

Результаты измерения:

quicksort: 0.265625s
sorted: 0.03125s

Как говорится, результат на лицо)
