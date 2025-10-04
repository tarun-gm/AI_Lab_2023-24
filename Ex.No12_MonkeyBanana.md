# Ex.No: 12  Planning –  Monkey Banana Problem
### DATE: 04.10.2025                                                                            
### REGISTER NUMBER : 212223060284
### AIM: 
To find the sequence of plan for Monkey Banana problem using PDDL Editor.
###  Algorithm:
Step 1:  Start the program <br> 
Step 2 : Create a domain for Monkey Banana Problem. <br> 
Step 3:  Create a domain by specifying predicates. <br> 
Step 4: Specify the actions GOTO, CLIMB, PUSH-BOX, GET-KNIFE, GRAB-BANANAS in Monkey Banana problem.<br>  
Step 5:   Define a problem for Monkey Banana problem.<br> 
Step 6:  Obtain the plan for given problem.<br> 
Step 7: Stop the program.<br> 
### Program:
```
(define (domain monkey)
  (:requirements :strips) [cite_start][cite: 81]
  (:constants monkey box knife bananas glass waterfountain) [cite_start][cite: 82]
  (:predicates (location ?x) [cite_start][cite: 83]
    (on-floor) [cite_start][cite: 84]
    (at ?m ?x) [cite_start][cite: 85]
    (hasknife) [cite_start][cite: 86]
    (onbox ?x) [cite_start][cite: 87]
    (hasbananas) [cite_start][cite: 88]
    (hasglass) [cite_start][cite: 89]
    (haswater) [cite_start][cite: 90]
  )

  (:action GO-TO
    [cite_start]:parameters (?x ?y) [cite: 93]
    [cite_start]:precondition (and (location ?x) (location ?y) (on-floor) (at monkey ?y)) [cite: 94]
    [cite_start]:effect (and (at monkey ?x) (not (at monkey ?y))) [cite: 95]
  )

  (:action CLIMB
    [cite_start]:parameters (?x) [cite: 97]
    [cite_start]:precondition (and (location ?x) (at box ?x) (at monkey ?x)) [cite: 98]
    [cite_start]:effect (and (onbox ?x) (not (on-floor))) [cite: 99]
  )

  (:action PUSH-BOX
    [cite_start]:parameters (?x ?y) [cite: 101]
    [cite_start]:precondition (and (location ?x) (location ?y) (at box ?y) (at monkey ?y) (on-floor)) [cite: 102, 106]
    [cite_start]:effect (and (at monkey ?x) (not (at monkey ?y)) (at box ?x) (not (at box ?y))) [cite: 108, 109]
  )

  (:action GET-KNIFE
    [cite_start]:parameters (?y) [cite: 112]
    [cite_start]:precondition (and (location ?y) (at knife ?y) (at monkey ?y)) [cite: 113]
    [cite_start]:effect (and (hasknife) (not (at knife ?y))) [cite: 114]
  )

  (:action GRAB-BANANAS
    [cite_start]:parameters (?y) [cite: 116]
    [cite_start]:precondition (and (location ?y) (hasknife) (at bananas ?y) (onbox ?y)) [cite: 117, 118]
    [cite_start]:effect (hasbananas) [cite: 119]
  )

  (:action PICKGLASS
    [cite_start]:parameters (?y) [cite: 122]
    [cite_start]:precondition (and (location ?y) (at glass ?y) (at monkey ?y)) [cite: 123]
    [cite_start]:effect (and (hasglass) (not (at glass ?y))) [cite: 124]
  )

  (:action GETWATER
    [cite_start]:parameters (?y) [cite: 126]
    [cite_start]:precondition (and (location ?y) (hasglass) (at waterfountain ?y) (at monkey ?y) (onbox ?y)) [cite: 127, 128, 129, 130]
    [cite_start]:effect (haswater) [cite: 131]
  )
)
```
### PROBLEM 1
```
(define (problem pb1)
  (:domain monkey) [cite_start][cite: 135]
  (:objects p1 p2 p3 p4 bananas monkey box knife) [cite_start][cite: 136]
  (:init 
    (location p1) [cite_start][cite: 137]
    (location p2) [cite_start][cite: 138]
    (location p3) [cite_start][cite: 139]
    (location p4) [cite_start][cite: 140]
    (at monkey p1) [cite_start][cite: 141]
    (on-floor) [cite_start][cite: 142]
    (at box p2) [cite_start][cite: 143]
    (at bananas p3) [cite_start][cite: 144]
    (at knife p4) [cite_start][cite: 145]
  )
  (:goal (and (hasbananas)[cite_start])) [cite: 147]
)
``` 

### Output/Plan:

<img width="605" height="584" alt="image" src="https://github.com/user-attachments/assets/26a658d7-959b-4faa-a6a8-06a21e75216d" />


### Result:
Thus the plan was found for the initial and goal state of given problem.
