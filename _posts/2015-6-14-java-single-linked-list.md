---
layout: post
author: 孙福生
title: Java 单向链表的操作
categories: Java 算法与数据结构
tags: Java
---

* 单向链表增删改查的操作
* 反转单向链表
* 判断单向链表是否有环

写一个大家都比较熟悉的数据结构：单向链表。
虽然java的API里面已经提供了单向链表的类，大家可以直接拿来用，但是通过自己的编写也更能够让我们了解其中实现的过程，而且我们还可以写一些比较个性化的方法作为属于自己的数据结构。这里主要是介绍一些常用结构里面都会用到的方法，以及链表具体是如何操作的。

单向链表（单链表）是链表的一种，其特点是链表的链接方向是单向的，对链表的访问要通过顺序从头部开始读取，链表是使用指针进行构造的列表，又称为结点列表。因为链表是由一个个结点组装起来的，其中每个结点都有指针成员变量指列表中的下一个结点，由head指针指向第一个成为表头的结点而终止于最后一个指向nuLL的指针。

#### 首先定义一个简单的Person类
里面仅仅包含两个元素姓名和年龄

***
	public class Person {

	    private String name;
	    private int age;
	
	    public Person(String name, int age) {
	        this.name = name;
	        this.age = age;
	    }

	    public String getName() {
	        return name;
	    }

	    public void setName(String name) {
	        this.name = name;
	    }

	    public int getAge() {
	        return age;
	    }

	    public void setAge(int age) {
	        this.age = age;
	    }

	    @Override
	    public String toString() {
	        return "Person [name=" + name + ", age=" + age + "]";
	    }
		
	}


#### 接下来构造PersonNode节点
PersonNode节点拥有一个Person对象和PersonNode节点的指针

***
	public class PersonNode {
	
	    private Person person;
	    private PersonNode nextNode;
		
	    public PersonNode(Person person, PersonNode nextNode) {
	        this.person = person;
	        this.nextNode = nextNode;
	    }

	    public Person getPerson() {
	        return person;
	    }

	    public void setPerson(Person person) {
	        this.person = person;
	    }

	    public PersonNode getNextNode() {
	        return nextNode;
	    }

	    public void setNextNode(PersonNode nextNode) {
	        this.nextNode = nextNode;
	    }

	}


#### 增加节点
新添加的节点指向头结点

***
    public void addNode(Person p) {
	    if (isEmpty()) {
	        head = new PersonNode(p, null);
	    } else {
	        head = new PersonNode(p, head);
	    }
	    size++;
	}

#### 删除节点

***
	public void deleteNode(String personName) {
	    //情况一：链表节点数为0
	    if (isEmpty()) {
	        return ;
	    }
	    //情况二：链表中无此节点
	    if (!contains(personName)) {
	        return ;
	    }
	    //情况三：链表中只有一个节点且匹配
	    if (size == 1) {
	        head = null;
	        size = 0;
	        return ;
	    }
	    //情况四：删除的是第一个链表节点
	    int index = 0;//被删除节点的索引
	    for (PersonNode pn = head; pn != null; pn = pn.getNextNode()) {
	        if (pn.getPerson().getName().equals(personName)) {
	            break;
	        } else {
	            index++;
	        }
	    }
	    if (index == 0) {
	        head = new PersonNode(head.getNextNode().getPerson(), head.getNextNode().getNextNode());
	        size--;
	        return ;
	    }
	    //情况五：删除的不是第一个链表节点
	    int preIndex = 0;//被删除节点的前一个节点的索引
	    for (PersonNode pn = head; pn != null; pn = pn.getNextNode()) {
	        if (preIndex == index - 1) {
	            if (index == size - 1) {//删除的节点是最后一个节点
	                pn.setNextNode(null);
	            } else {
	                pn.setNextNode(pn.getNextNode().getNextNode());
	            }
	            size--;
	            return ;
	        }
	        preIndex++;
	    }
	}
	
#### 修改人名

***
	public void modifyPersonName(String oldName, String newName) {
		if (newName == null || oldName == null) {
			return ;
		}
		Person p = searchPerson(oldName);
		if (p == null) {
			return ;
		}
		p.setName(newName);
	}
	
#### 修改年龄

***
	public void modifyPersonAge(String personName, int age) {
		if (personName == null) {
			return ;
		}
		if (age <= 0) {
			return ;
		}
		Person p = searchPerson(personName);
		if (p == null) {
			return ;
		}
		p.setAge(age);
	}
	
#### 根据人名搜索这个人的信息

***
	public Person searchPerson(String personName) {
		for (PersonNode pn = head; pn != null; pn = pn.getNextNode()) {
			if (pn.getPerson().getName().equals(personName)) {
				return pn.getPerson();
			}
		}
		return null;
	}
	
#### 根据人名搜索这个人的节点

***
	public PersonNode searchNode(String personName) {
		for (PersonNode pn = head; pn != null; pn = pn.getNextNode()) {
			if (pn.getPerson().getName().equals(personName)) {
				return pn;
			}
		}
		return null;
	}
	
#### 链表中是否包含该名字的节点

***
	public boolean contains(String personName) {
		if (!isEmpty()) {
			for (PersonNode pn = head; pn != null; pn = pn.getNextNode()) {
				if (pn.getPerson().getName().equals(personName)) {
					return true;
				}
			}
		}
		return false;
	}
	
#### 是否为空

***
	public boolean isEmpty() {
		return size == 0;
	}
	
#### 打印所有节点

***
	public void printAllNode() {
		if (!isEmpty()) {
			for (PersonNode pn=head; pn != null; pn = pn.getNextNode()) {
				System.out.println(pn.getPerson().toString());
			}
		}
	}
	
#### 链表反转

***
	public PersonNode reverse() {
		if (isEmpty()) {
			return null;
		}
		PersonNode pre = head;
		PersonNode cur = head.getNextNode();
		PersonNode next;
		while (cur != null) {
			next = cur.getNextNode();
			cur.setNextNode(pre);
			pre = cur;
			cur = next;
		}
		head.setNextNode(null);
		head = pre;
		return head;
	}

#### 判断链表是否为环链表
两个指针slow，fast都从头开始遍历单链表，slow每次向前走1步，fast每次向前走2步，如果fast碰到了null，说明环不存在；如果fast碰到本应在身后的slow说明环存在。

***
	public boolean isLoop() {
		if (head == null || head.getNextNode() == null) {
			return false;
		}
		PersonNode slow = head;
		PersonNode fast = head;
		while (fast != null && fast.getNextNode() != null) {
			slow = slow.getNextNode();
			fast = fast.getNextNode().getNextNode();
			if (slow == fast) {
				return true;
			}
		}
		return false;
	}

详细内容请参考[GitHub源码](https://github.com/sfsheng0322/LinkedListProject)

