Data Structure
=============

由 NeetCode75 所整理的常用 DS，本篇採 Java 作為主要使用的程式語言

[TOC]

# Implement Data Structures
## Design Dynamic Array (Resizable Array)
> 注意
  - The index `i` provided to `get(int i)` and `set(int i)` is guranteed to be greater than or equal to 0 and less than the number of elements in the array.
```java
class DynamicArray {
  private int[] arr;
  // 陣列中的實際元素個數
  private int size;

  public DynamicArray(int capacity) {
    this.arr = new int[capacity];
    this.size = 0;
  }

  // Get value at the i-th index
  public int get(int i) {
    if (i < 0 || i > size-1) {
      throw new IndexOutOfBoundsException("Index is illegal.");
    }

    return arr[i];
  }
  
  // Insert value n at the i-th index
  public void set(int i, int n) {
    if (i < 0 || i > size-1) {
      throw new IndexOutOfBoundsException("Index is illegal.");
    }
    arr[i] = n;
  }

  // Insert n in the last position of the array
  public void pushback(int n) {
    if (size == arr.length) {
      resize();
    }
    arr[size++] = n;
  }

  // Remove the last element in the array
  public int popback() {
    if (size == 0) {
      throw new IllegalStateException("Cannot pop from an empty array.");
    }

    return arr[--size];
  }

  // Resize the array
  private void resize() {
    int newCapacity = arr.length * 2;
    int[] newArr = new int[newCapacity];
    System.arraycopy(arr, 0, newArr, 0, this.size);
    arr = newArr;
  }

  // Get the size of the array
  public int getSize() {
	  return size;
  }

  // Get the capacity of the array
  public int getCapacity() {
	  return arr.length;
  }
}
```

## Design Singly Linked List
## Design Double-ended Queue
## Design Binary Search Tree
## Design Hash Table
## Design Heap
## Design Graph
## Design Disjoint Set (Union-Find)
## Design Segment Tree