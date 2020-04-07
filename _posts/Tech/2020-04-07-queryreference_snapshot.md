---
layout: post
title: Compare QueryReference and Snapshot in firestore
categories: Tech-Post Firebase
description: Compare QueryReference and Snapshot in firestore
keywords: Firebase Firestore query reference snapshot
---

## Two objects from firestore
When we send query to firestore to give us somthing from the database. Firestore return us two types of objects: QueryReference object and Snapshot object. Of these objects, they can be either Document or Collection versions.
**Firestore will always return us these objects, even if nothing exists at from that query**.

* QueryReference Object
  * DocumentReference
  * CollectionReference
* SnapshotObject
  * DocumentSnapshot
  * CollectionSnapshot(which is called QuerySnapshot)

## QueryReference Object
QueryReference object is an object that represents the **'current' place in the database that we querying**.

We get them by calling either:
```javaScript
firestore.doc('/users/:userId')
firestore.collections('/users')
```

The queryReference object **does not have the actuall data of the collection or document**. it instead has properties that tell us detail about it, or the method to get the snapshot object which gives us the data we looking for.

## DocumentReference vs CollectionReference
We use documentReference objects to perform our CRUD methods(create, retrieve, update, delete).The documentReference methods are .set(), .get(), .update() and .delete() respectively

We can also add documents to collections using the collectionReference object using the .add() method (collectionRef.add(value:prop))

## Snapshot Object
We get the snapshot object from the referenceObject using the .get() method. ie. documentRef.get() or collectionRef.get()

* documentRef returns a documentSnapshot object
* collectionRef return a querySnapshot object

## DocumentSnapshot
We get a documentSnapshot object from our documentReference object.

* The documentSnapshot object allow us to check if a document exists at this query using the .exists property which returns a boolean.
* We can also get the actual properties on the object by calling the .data() method, which returns us a JSON object of the document

## QuerySnapshot
We get a querySnapshot object from out collectionReference objcet.
* We can check if there are any documents in the collection by calling the .empty property which returns a boolean.
* We can get all the documents in the collection by calling the .docs property. It returns an array of our documents as documentSnapshot objects.
