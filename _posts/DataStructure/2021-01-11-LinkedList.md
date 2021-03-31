---
layout: post
title:  "Linked List"
excerpt: ""

categories:
  - DataStructure
tags:
  - [Java, 자료구조, Linked List]

toc: true
toc_sticky: true
comments: true
 
date: 2021-01-11
last_modified_at: 2021-01-11
---
## Linked List

Node 또는 Vertax라 불리는 자료형이 연결된 형태의 자료구조이다.

일반적으로 Node는 자신의 Data인 Value값과 자신의 다음 Node를 가리키는 포인터값(편의상 Next라고 하겠다)으로 이루어져 있다.

Linked List의 장점은 삽입 및 삭제가 빠르다는 것인데, 삽입/삭제 행위의 시간복잡도는 O(1)이다.

단순히 추가하려는 위치의 Node의 Next값을 새로운 노드로 바꿔주거나, 지우려는 Node이전의 Next값을 바꿔주는 식으로 삽입/삭제가 이루어지기 때문이다.

(배열은 삽입/삭제 시 전체 Element를 옮겨줘야해서 시간복잡도가 O(n)이다.)

<br>

그러나 큰 착각을 할 수 있는게 Linked List는 맨 앞 요소부터 순차접근을 해야하기 때문에, 특정 위치에 삽입하거나 삭제하는 경우, 해당 위치를 찾아가는데 O(n)이 소요되어서 실질적으로 1.찾아서 2.삽입/삭제한다 의 행위는 O(n)이 걸린다고 할 수 있다.

<br>

## Single Linked List

![](/public/img/LinkedList/1.jpeg)

```java
import java.util.ArrayList;
import java.util.List;

public class SingleLinkedList<T>{
    private Node head;
    private int size;

    public SingleLinkedList(){
        head = new Node(null);
        size = 0;
    }

    public int getSize(){
        return size;
    }

    private void add(Node node, Node newNode) {
        newNode.next = node.next;
        node.next = newNode;

        size++;
    }

    public void add(T val) {
        Node curNode = head;
        while(curNode.next != null) {
            curNode = curNode.next;
        }
        add(curNode, new Node(val));
    }

    public void add(int index, T val) throws IndexOutOfBoundsException {
        if(index < 0)
            throw new IndexOutOfBoundsException("Index can't be lower than 0");

        Node curNode = head;
        int curIdx = -1;
        while(curNode.next != null && curIdx < index-1) {
            curNode = curNode.next;
            curIdx++;
        }
        add(curNode, new Node(val));
    }

    private void remove(Node prev) {
        prev.next = prev.next.next;

        size--;
    }

    public boolean remove(T val) {
        Node curNode = head;
        while(curNode.next != null) {
            if(curNode.next.value.equals(val))
            {
                remove(curNode);
                return true;
            }
            curNode = curNode.next;
        }
        return false;
    }

    public List<T> getList() {
        ArrayList<T> list = new ArrayList<>();
        Node curNode = head.next;
        while(curNode != null) {
            list.add(curNode.value);
            curNode = curNode.next;
        }
        return list;
    }

    protected class Node {
        private T value;
        private Node next;

        private Node(T value) {
            this.value = value;
        }

        protected T getValue(){ return value; }
        protected Node getNext(){ return next; }
    }
}
```



## Doubly Linked List

![](/public/img/LinkedList/2.jpeg)

```java
import java.util.ArrayList;
import java.util.List;

public class DoubleLinkedList<T>{
    private Node head;
    private Node tail;
    int size;

    public DoubleLinkedList(){
        head = new Node(null);
        tail = new Node(null);
        size = 0;
    }

    public int getSize(){
        return size;
    }

    private void add(Node node, Node newNode) {
        newNode.next = node.next;
        newNode.prev = node;
        if(newNode.next != null)
            newNode.next.prev = newNode;
        if(newNode.prev != null)
            newNode.prev.next = newNode;

        if(newNode.next == null)
            tail.next = newNode;

        size++;
    }

    public void add(T val) {
        if(tail.next != null)
            add(tail.next, new Node(val));
        else
            add(head, new Node(val));
    }

    public void add(int index, T val) throws IndexOutOfBoundsException {
        if(index < 0)
            throw new IndexOutOfBoundsException("Index can't be lower than 0");

        Node curNode = head;
        int curIdx = -1;
        while(curNode.next != null && curIdx < index-1) {
            curNode = curNode.next;
            curIdx++;
        }
        add(curNode, new Node(val));
    }

    private void remove(Node node) {
        if(node.next == null)
            tail.next = node.prev;

        if(node.prev != null)
            node.prev.next = node.next;
        if(node.next != null)
            node.next.prev = node.prev;
        node.next = null;
        node.prev = null;

        size--;
    }

    public boolean remove(T val) {
        Node curNode = head.next;
        while(curNode != null) {
            if(curNode.value.equals(val))
            {
                remove(curNode);
                return true;
            }
            curNode = curNode.next;
        }
        return false;
    }

    public List<T> getList() {
        ArrayList<T> list = new ArrayList<>();
        Node curNode = head.next;
        while(curNode != null) {
            list.add(curNode.value);
            curNode = curNode.next;
        }
        return list;
    }

    protected class Node {
        private T value;
        private Node next;
        private Node prev;

        private Node(T value) {
            this.value = value;
        }

        protected T getValue(){ return value; }
        protected Node getNext(){ return next; }
        protected Node getPrev(){ return prev;}
    }
}
```

## Circular Linked List

![](/public/img/LinkedList/3.jpeg)

## Doubly Circular Linked List

![](/public/img/LinkedList/4.jpeg)

```java
import java.util.ArrayList;
import java.util.List;

public class DoublyCircularLinkedList<T>{
    protected Node head;
    private int size;

    public DoublyCircularLinkedList(){
        head = new Node(null);
        head.next = head;
        head.prev = head;
    }

    public int getSize(){
        return size;
    }

    private void add(Node node, Node newNode) {
        newNode.next = node.next;
        newNode.prev = node;
        newNode.next.prev = newNode;
        newNode.prev.next = newNode;

        size++;
    }

    public void add(T val) {
        add(head.prev, new Node(val));
    }

    public void add(int index, T val) throws IndexOutOfBoundsException {
        if(index < 0)
            throw new IndexOutOfBoundsException("Index can't be lower than 0.");

        Node curNode = head;
        int curIdx = -1;
        while(curNode.next != head && curIdx < index-1) {
            curNode = curNode.next;
            curIdx++;
        }
        add(curNode, new Node(val));
    }

    private void remove(Node node){
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = null;
        node.prev = null;

        size--;
    }

    public boolean remove(T val){
        Node curNode = head.next;
        while(curNode != head) {
            if(curNode.value.equals(val)){
                remove(curNode);
                return true;
            }
            curNode = curNode.next;
        }
        return false;
    }

    public List<T> getList(){
        List<T> list = new ArrayList<>();
        Node curNode = head.next;
        while(curNode != head) {
            list.add(curNode.value);
            curNode = curNode.next;
        }
        return list;
    }

    protected class Node{
        private T value;
        private Node next;
        private Node prev;

        protected Node(T value) {
            this.value = value;
            next = null;
        }

        protected T getValue(){ return this.value; }
        protected Node getNext(){ return this.next; }
        protected Node getPrev(){ return this.prev; }
    }
}
```

