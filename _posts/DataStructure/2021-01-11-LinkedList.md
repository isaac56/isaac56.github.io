---
layout: post
title:  "Linked List"
excerpt: ""

categories:
  - Data Structure
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

일반적으로 Node는 자신의 Data인 Value값과 자신의 다음 Node를 가리키는 포인터값(편의상 Next라고 하겠다.)으로 이루어져 있다.

Linked List의 장점은 삽입 및 삭제가 빠르다는 것인데, 삽입/삭제 행위의 시간복잡도는 O(1)이다.

단순히 추가하려는 위치의 Node의 Next값을 새로운 노드로 바꿔주거나, 지우려는 Node이전의 Next값을 바꿔주는 식으로 삽입/삭제가 이루어지기 때문이다.

(배열은 삽입/삭제 시 전체 Element를 옮겨줘야해서 시간복잡도가 O(n)이다.)

<br>

그러나 큰 착각을 할 수 있는게 Linked List는 맨 앞 요소부터 순차접근을 해야하기 때문에, 특정 위치에 삽입하거나 삭제하는 경우, 해당 위치를 찾아가는데 O(n)이 소요되어서 실질적으로 1.찾아서 2.지운다 의 행위는 O(n)이 걸린다고 할 수 있다.

<br>

## Doubly Circular Linked List 구현 - Java

- 개념만 아는 상태에서 어떤 코드도 참조하지 않고 한번 구현해보았다. (add, remove만 구현)
- 코드가 지저분하여 너무 찜찜하다 ㅠㅠ
- Java Collections에 있는 LinkedList와 API를 동일하게 구성하여 JUnit으로 테스트해보았다.

```java
public class DoublyCircularLinkedList<T> {
    protected Node head;

    private void add(Node node, Node toAdd) {
        toAdd.next = node.next;
        toAdd.prev = node;
        toAdd.next.prev = toAdd;
        toAdd.prev.next = toAdd;
    }

    public void add(T val) {
        Node toAdd = new Node(val);
        if(head == null) {
            toAdd.next = toAdd;
            toAdd.prev = toAdd;
            head = toAdd;
        }
        else{
            add(head.prev, toAdd);
        }
    }

    public void add(int index, T val) {
        if(index < 0)
            return;
        if(head == null) {
            add(val);
            return;
        }

        Node toAdd = new Node(val);
        if(index == 0) {
            add(head.prev, toAdd);
            head = toAdd;
            return;
        }

        Node curNode = head;
        int curIdx = 0;
        while(curNode.next != head && curIdx < index-1) {
            curNode = curNode.next;
            curIdx++;
        }
        add(curNode, toAdd);
    }

    private void remove(Node node){
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = null;
        node.prev = null;
    }

    public void remove(T val){
        if(head == null)
            return;
        Node curNode = head;
        while(curNode.next != head){
            if(curNode.value.equals(val)){
                break;
            }
            curNode = curNode.next;
        }
        if(curNode.value.equals(val)){
            if(curNode == head) {
                if(curNode.next == head)
                    head = null;
                else
                    head = curNode.next;
            }
            remove(curNode);
            return;
        }
    }

    protected class Node{
        private T value;
        private Node next;
        private Node prev;

        public Node(T value) {
            this.value = value;
            next = null;
        }

        protected T getValue(){
            return this.value;
        }

        protected Node getNext(){
            return this.next;
        }

        protected Node getPrev(){
            return this.prev;
        }
    }
}
```



# 그림으로 보면 이해가 쉬울 것 같아서 그림만 넣었다.

## Single Linked List

![](/public/img/LinkedList/1.jpeg)

## Doubly Linked List

![](/public/img/LinkedList/2.jpeg)

## Circular Linked List

![](/public/img/LinkedList/3.jpeg)

## Doubly Circular Linked List

![](/public/img/LinkedList/4.jpeg)

