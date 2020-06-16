#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Tue Jun 16 20:52:57 2020

@author: L Mongiat
"""
#Lecture d'un fichier CSV et récupération des données dans une liste de tuples
import csv
f=open("fausse1ere.csv","r")
data=[tuple(ligne.rstrip().split(';')) for ligne in f]
f.close()

def listeSpe(table):
    """
    Liste des spécialités à partir du fichier de données présenté dans une liste de tuple
    Les spécialités sont dans les colonnes 6,7,8
    la liste est de la forme [sp1,sp2,..., spN]
    """
    liste=[]
    for j in [6,7,8]:
        for i in range(1,len(table)):
            if not(table[i][j] in liste)and table[i][j]!='' :
                liste.append(table[i][6])
    liste.sort()
    liste.reverse()
    return liste

def couplesPossibles(liste):
    """
    Liste des couples des spécialités à partir d'une liste de spécialité à une dimension
    retourne un dictionnaire de tuples de 2 spé
    """
    couples={}
    for i in range(0,len(liste)):
        for j in range(i+1,len(liste)):
            couples[(liste[i],liste[j])]=[]
    return couples

def triplesPossibles(liste):
    """
    Liste des triplettes de spécialités à partir d'une liste de spécialité à une dimension
    retourne un dictionnaire de tuples de 3 spé
    """
    triples={}
    for i in range(0,len(liste)):
        for j in range(i+1,len(liste)):
            for k in range(j+1,len(liste)):
                triples[(liste[i],liste[j],liste[k])]=[]
    return triples

#Vérification si un élève fait deux spécialités
def fait2SPE(i,sp1,sp2):
    """
    Vérifie qu'un élève de data de numéro i, fait les 2 spé parmi les colonnes 6,7,8
    """
    if sp1!=sp2 and(sp2==data[i][6] or sp2==data[i][7]or sp2==data[i][8] ) and (sp1==data[i][6] or sp1==data[i][7] or sp1==data[i][8] ):
        return True
    return False

def fait3SPE(i,spe1,spe2,spe3):
    """
    Vérifie qu'un élève de data de numéro i, fait les 3 spé parmi les colonnes 6,7,8
    """
    if (spe1==data[i][6] or spe1==data[i][7] or spe1==data[i][8]) and (spe2==data[i][6] or spe2==data[i][7] or spe2==data[i][8]) and (spe3==data[i][6] or spe3==data[i][7] or spe3==data[i][8]):
         return True
    return False

def ListeEffectif2(dico):
    """
    donne pour chaque couples de spé la liste des numéros d'élèves faisant ces 2 spé
    sous forme d'un dico (sp1,sp2) [k,l,m...]
    """
    for key,value in dico.items():
            for i in range(1,len(data)):
                if fait2SPE(i,key[0],key[1]):
                    value.append(i)
    nettoyer(dico)
    return None

def ListeEffectif3(dico):
    """
    donne pour chaque triplettes de spé la liste des numéros d'élèves faisant ces 3 spé
    sous forme d'un dico (sp1,sp2,sp3) [k,l,m...]
    """
    for key,value in dico.items():
            for i in range(1,len(data)):
                if fait3SPE(i,key[0],key[1],key[2]):
                    value.append(i)
    nettoyer(dico)
    return None

def nettoyer(couples):
    """nettoyage des groupes vides présents dans le dico 
    """
    retirer=[]
    for key,value in couples.items():
        if len(value)==0:
            retirer.append(key)
    for i in retirer:
        del couples[i]
    return couples
    

def comptageEffectif(dico):
    """
    prend le dico des couples de spé possibles et compte le nombre d'élèves sous
    forme d'un dico (sp1,sp2), nbre d'élèves
    """
    groupes={}
    for key,value in dico.items():
        if len(value) >0:
            groupes[key]=len(value)
    return groupes
    
def afficheListeNom(liste):
    print("Voici les",len(liste),"élèves concernés par votre critère")
    for i in range(len(liste)):
        print(data[liste[i]][0],"--",data[liste[i]][1]," ",data[liste[i]][2])     

def rechercheEleve2SPE(spe1,spe2,LV2):
    liste=[]
    for i in range(1,len(data)):
        if fait2SPE(i,spe1,spe2) and data[i][5]==LV2:
            liste.append(i)
    return liste


#Programme principal
listespe=listeSpe(data)     #liste des spécialités

#pour les terminales avec 2 spécialités
couples=couplesPossibles(listespe)  #recensement des couples possibles
ListeEffectif2(couples) #fait la liste des éleves dans les couples de spé
groupesTerm=comptageEffectif(couples) #Donne le nombre d'élèves par couple de spé

#pour les premières avec trois Spécialités
triplettes=triplesPossibles(listespe)
ListeEffectif3(triplettes)
groupesPrem=comptageEffectif(triplettes)

