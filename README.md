# python3
Python program that reads an input stream defining a family tree and determines different specializations of related between people in the tree.


#!/usr/bin/python3
# Allana Metz
# Project 2

import sys

tree = {}  # initializes tree

input = sys.stdin.readline()  # reads first line of input


def addper(name):  # method to add new person to tree with blank subdictionaries and lists
    tree.update({name: {'p1': None, 'p2': None, 'SpouseList': [], 'Children': []}})
    # p1 and p2 are the parents of the person
    # name is the key to which the persons attributes are mapped


def setParents(name, person1, person2):  # sets parents and children when someone is added
    tree[name]['p1'] = person1
    tree[name]['p2'] = person2

    tree[person1]['Children'].append(name)
    tree[person2]['Children'].append(name)


def setSpouse(name, spouse):
    tree[name]['SpouseList'].append(spouse)  # appends spouse to a list


def listSpouse(name):
    if tree[name]['SpouseList'] != None:
        temp = list(set(tree[name]['SpouseList']))  # removes duplicates in spouselist
        temp.sort()  # sorts the spouselist
        print("\n" + input + "\n".join(temp))  # prints each element with a new line inbetween
    else:
        print("\n" + input)


def isSib(name1, name2):  # method to check if the two share a parent
    if (tree[name1]['p1'] == None or tree[name2]['p2'] == None):
        print("\n" + input + "No")
    elif tree[name1]['p1'] == tree[name2]['p1'] or tree[name1]['p2'] == tree[name2]['p2']:
        print(input + "Yes")
    elif tree[name1]['p1'] == tree[name2]['p2'] or tree[name1]['p2'] == tree[name2]['p1']:
        print("\n" + input + "Yes")
    else:
        print("\n" + input + "No")


def printSibs(name):
    p1 = tree[name]['p1']
    p2 = tree[name]['p2']

    if p1 == None or p2 == None:
        print("\n" + input)
    else:
        temp = (tree[p1]['Children'])
        temp = temp + (tree[p2]['Children'])  # add the children of each parent together
        temp = list(set(temp))
        temp.remove(name)
        temp.sort()
        print("\n" + input + "\n".join(temp))


def printChildren(name):
    temp = tree[name]['Children']
    temp = list(set(temp))  # removes duplicates
    temp.sort()
    print("\n" + input + "\n".join(temp))


def isAncestor(name1, name2):
    return recurAncestors(name1, tree[name2]['p1'], tree[name2]['p2'])


def recurAncestors(name1, p1, p2):
    if (p1 is not None):  # if the person name1 has parents
        if name1 == p1 or name1 == p2:
            return True
        else:
            return recurAncestors(name1, tree[p1]['p1'], tree[p1]['p2']) or recurAncestors(name1, tree[p2]['p1'],
                                                                                           tree[p2]['p2'])
    return False


def listAncestors(name1, ancestors):  # creats a list of all ancestors of a person recrusively
    myList = ancestors  # ancestors is the list that gets passed through recursive calls
    if (tree[name1]['p1'] is not None):
        myList.append(tree[name1]['p1'])  # adds parents to ancestor list
        myList.append(tree[name1]['p2'])
        return listAncestors(tree[name1]['p1'], myList) and listAncestors(tree[name1]['p2'], myList)
    else:
        myList = list(set(myList))  # remove duplicates
        return myList


def printAncestors(name):
    temp = list(set(listAncestors(name, [])))  # prints listAncestors and sorts it
    temp.sort()
    print("\n" + input + "\n".join(temp))


def isCousin(name1, name2):
    list1 = listAncestors(name1, [])
    list2 = listAncestors(name2, [])

    if (not set(list1).isdisjoint(list2)):  # tests if they share a common ancestor
        if (name1 not in list2) and (name2 not in list1):  # checks to see if they are ancestors of each other
            return True
    else:
        return False


def printCousins(name):
    temp = []
    for key in tree.keys(): #for everone in tree
        if (isCousin(name, key)): #see if they are cousins
            temp.append(key) #if so append that person to a list temp
    temp2 = list(set(temp))
    if name in temp2: #removes someone if their cousin list contains them
        temp2.remove(name)
    temp2.sort()
    print("\n" + input + "\n".join(temp2))


def isUnrelated(name1, name2):
    list1 = listAncestors(name1, [])
    list2 = listAncestors(name2, [])

    if (name1 == name2): #see if they are the same person
        return True
    elif (set(list1).isdisjoint(list2)):
        if ((not list1.__contains__(name2)) and not (list2.__contains__(name1))): #if they do not share a common ancestor
            return True
    else:
        return False


def listUnrelated(name1): #checks everone in tree and tests if they're unrelated to name1
    temp = []
    for key in tree.keys():
        if (isUnrelated(name1, key)):
            temp.append(key)
    temp2 = list(set(temp))
    temp2.sort()
    print("\n" + input + "\n".join(temp2))


while len(input) > 2: #while we have input

    arg = input.rstrip() #removes whitespace
    args = arg.split(" ")
    args[-1] = args[-1].strip('\n')# if there is a new line at the end remove it
    
    if args[0] == 'E':
        groom = args[1]
        bride = args[2]

        if tree.__contains__(groom) != True: #if the person is new add them to tree
            addper(groom)
            setSpouse(groom, bride)
        else:
            setSpouse(groom, bride)

        if tree.__contains__(bride) != True:
            addper(bride)
            setSpouse(bride, groom)
        else:
            setSpouse(bride, groom)
        if len(args) == 4: #if we have a third person
            kid = args[3]
            if tree.__contains__(kid) != True:
                addper(kid)
                setParents(kid, groom, bride)
            setParents(kid, groom, bride)

    if args[0] == "X":
        person1 = args[1]
        relation = args[2]
        person2 = args[3]

        if tree.__contains__(person1) != True:
            addper(person1)
        if tree.__contains__(person2) != True:
            addper(person2)

        if relation == "child":
            if tree[person1]['p1'] == person2 or tree[person1]['p2'] == person2:
                print("\n" + input + "Yes")
            else:
                print("\n" + input + "No")

        if relation == "spouse":
            if person2 in tree[person1]['SpouseList']:
                print("\n" + input + "Yes")
            else:
                print("\n" + input + "No")

        if relation == "sibling":
            isSib(person1, person2)

        if relation == "ancestor":
            if isAncestor(person1, person2) == True:
                print("\n" + input + 'Yes')
            else:
                print("\n" + input + "No")

        if relation == "cousin":
            if isCousin(person1, person2) == True:
                print("\n" + input + "Yes")
            else:
                print("\n" + input + "No")

        if relation == "unrelated":
            if isUnrelated(person1, person2) == True:
                print("\n" + input + "Yes")
            else:
                print("\n" + input + "No")

    if args[0] == "W":
        relation = args[1]
        person1 = args[2]

        if tree.__contains__(person1) != True:
            addper(person1)

        if relation == "child":
            printChildren(person1)

        if relation == "spouse":
            listSpouse(person1)

        if relation == "sibling":
            printSibs(person1)

        if relation == "ancestor":
            printAncestors(person1)

        if relation == "cousin":
            printCousins(person1)

        if relation == "unrelated":
            listUnrelated(person1)
    input = sys.stdin.readline() #reads the next line of input before looping back
