

---

### 1. **Пузырьковая сортировка (Bubble Sort)**:
- **Описание**: Повторяется проход по списку, сравниваются соседние элементы и меняются местами, если они стоят в неправильном порядке.
- **Сложность**: O(n²) в худшем и среднем случае, O(n) — в лучшем случае (когда массив уже отсортирован).
- **Плюсы**: Простая в реализации.
- **Минусы**: Неэффективна для больших массивов.

```java
public class BubbleSort {
    public static void bubbleSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    // Обмен местами
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }
}
```

---

### 2. **Сортировка вставками (Insertion Sort)**:
- **Описание**: Элементы массива последовательно вставляются на своё место в уже отсортированную часть списка.
- **Сложность**: O(n²) в худшем и среднем случае, O(n) — в лучшем (когда массив почти отсортирован).
- **Плюсы**: Эффективна для небольших или почти отсортированных массивов.
- **Минусы**: Неэффективна для больших массивов.

```java
public class InsertionSort {
    public static void insertionSort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;
            // Перемещаем элементы, которые больше ключа
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j = j - 1;
            }
            arr[j + 1] = key;
        }
    }
}
```

---

### 3. **Сортировка выбором (Selection Sort)**:
- **Описание**: На каждом шаге находит минимальный элемент и ставит его на своё место.
- **Сложность**: O(n²) в худшем, среднем и лучшем случаях.
- **Плюсы**: Простота реализации, меньшее число перестановок.
- **Минусы**: Неэффективна для больших массивов.

```java
public class SelectionSort {
    public static void selectionSort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }
            // Обмен местами
            int temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }
}
```

---

### 4. **Сортировка слиянием (Merge Sort)**:
- **Описание**: Делит массив пополам, рекурсивно сортирует части, затем сливает отсортированные части.
- **Сложность**: O(n log n) в худшем, среднем и лучшем случаях.
- **Плюсы**: Стабильна, эффективна для больших массивов.
- **Минусы**: Требует дополнительной памяти для хранения временных массивов.

```java
public class MergeSort {
    public static void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int middle = (left + right) / 2;
            mergeSort(arr, left, middle);
            mergeSort(arr, middle + 1, right);
            merge(arr, left, middle, right);
        }
    }

    private static void merge(int[] arr, int left, int middle, int right) {
        int n1 = middle - left + 1;
        int n2 = right - middle;

        int[] L = new int[n1];
        int[] R = new int[n2];

        for (int i = 0; i < n1; i++)
            L[i] = arr[left + i];
        for (int j = 0; j < n2; j++)
            R[j] = arr[middle + 1 + j];

        int i = 0, j = 0;
        int k = left;
        while (i < n1 && j < n2) {
            if (L[i] <= R[j]) {
                arr[k] = L[i];
                i++;
            } else {
                arr[k] = R[j];
                j++;
            }
            k++;
        }

        while (i < n1) {
            arr[k] = L[i];
            i++;
            k++;
        }

        while (j < n2) {
            arr[k] = R[j];
            j++;
            k++;
        }
    }
}
```

---

### 5. **Быстрая сортировка (Quick Sort)**:
- **Описание**: Выбирается опорный элемент, и массив разделяется на две части — элементы, меньшие и большие опорного, затем сортировка применяется рекурсивно к обеим частям.
- **Сложность**: O(n²) в худшем случае, O(n log n) в среднем и лучшем случаях.
- **Плюсы**: Быстрая на практике, особенно для случайных массивов.
- **Минусы**: Неустойчива, сложность в худшем случае O(n²), но это можно улучшить выбором случайного опорного элемента.

```java
public class QuickSort {
    public static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }

    private static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = (low - 1);
        for (int j = low; j < high; j++) {
            if (arr[j] < pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;
        return i + 1;
    }
}
```

---

### 6. **Пирамидальная сортировка (Heap Sort)**:
- **Описание**: Преобразует массив в двоичную кучу (heap), затем извлекает максимальный элемент и перестраивает кучу.
- **Сложность**: O(n log n) в худшем, среднем и лучшем случаях.
- **Плюсы**: Не требует дополнительной памяти, гарантированная сложность O(n log n).
- **Минусы**: Сложнее реализовать, не является стабильной сортировкой.

```java
public class HeapSort {
    public static void heapSort(int[] arr) {
        int n = arr.length;

        for (int i = n / 2 - 1; i >= 0; i--)
            heapify(arr, n, i);

        for (int i = n - 1; i >= 0; i--) {
            int temp = arr[0];
            arr[0] = arr[i];
            arr[i] = temp;
            heapify(arr, i, 0);
        }
    }

    private static void heapify(int[] arr, int n, int i) {
        int largest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;

        if (left < n && arr[left] > arr[largest])
            largest = left;

        if (right < n && arr[right] > arr[largest])
            largest = right;

        if (largest != i) {
            int swap = arr[i];
            arr[i] = arr[largest];
            arr[largest] = swap;
            heapify(arr, n, largest);
        }
    }
}
```

---

### 7. **Поразрядная сортировка (Radix Sort)**:
- **Описание**: Сортирует элементы по разрядам (цифрам или битам), начиная с младших.
- **Сложность**: O(d * (n + k)), где d — количество разрядов, k — количество возможных значений для одного разряда.
- **Плюсы**: Эффективен для данных с ограниченным диапазоном (например, целых чисел).
- **Минусы**: Ограничен специфическими типами данных.

```java
import java.util.Arrays;

public class RadixSort {
    public static void radixSort(int[] arr) {
        int max = Arrays.stream(arr).max().getAsInt();
        for (int exp = 1; max / exp > 0; exp *= 10) {
            countSort(arr, exp);
        }
    }

    private static void count

Sort(int[] arr, int exp) {
        int n = arr.length;
        int[] output = new int[n];
        int[] count = new int[10];

        for (int i = 0; i < n; i++)
            count[(arr[i] / exp) % 10]++;

        for (int i = 1; i < 10; i++)
            count[i] += count[i - 1];

        for (int i = n - 1; i >= 0; i--) {
            output[count[(arr[i] / exp) % 10] - 1] = arr[i];
            count[(arr[i] / exp) % 10]--;
        }

        for (int i = 0; i < n; i++)
            arr[i] = output[i];
    }
}
