---
layout: post
title: Java annotation的继承性
categories: [java annotation]
---
Java annotation只能从超类继承不能从接口继承

[Javadoc](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/Inherited.html)

>Indicates that an annotation type is automatically inherited. If an Inherited meta-annotation is present on an annotation type declaration, and the user queries the annotation type on a class declaration, and the class declaration has no annotation for this type, then the class's superclass will automatically be queried for the annotation type. This process will be repeated until an annotation for this type is found, or the top of the class hierarchy (Object) is reached. If no superclass has an annotation for this type, then the query will indicate that the class in question has no such annotation. Note that this meta-annotation type has no effect if the annotated type is used to annotate anything other than a class. Note also that this meta-annotation only causes annotations to be inherited from superclasses; annotations on implemented interfaces have no effect.
