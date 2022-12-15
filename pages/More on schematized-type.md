- In my last post [[Type and Schema: Towards a finer data "shape" control]] I talked about how type and schema is actually one. Here I want to put some code of the language in my imagination, and let you see how powerful it would be. Let's name this language "psil". The syntax is pretty bad but it doesn't prevent me from laying out the idea
- Now assume in psil we have these builtin-types: `type`, `str`, `int`, `sym`, `bool` and `kw`(keyword, just as clojure) first thing you want to do is checking the type of the value:
- ```clj
  (type 1) ;=> int
  (int? 1) ;=> true
  (str? 1) ;=> false
  ```
- In addition to regular type, we also have schema type:
- ```clj
  ; some common schema types
  
  %[:string {:min 1 :max 10}] ; string with length limit
  %[:seq int]                  ; a homogenious sequence of int
  %[:seq _]                    ; a heterogenious sequence of anything
  %even                        ; function that take one value and return a boolean can be used as well
  ; the following two type is equivalent
  str
  %[:string]
  ; function type
  %[:=> [:cat int?] int?] ; a function taking an int and return an int
  %[:=> [:cat str? str?] str?] ; a function taking 2 string and return a string
  ```
- Fancy stuff with type is allowed:
- ```clj
  ;; assume we have this function
  (defn validate [t v] 
   (comment omitted)) 
  
  (validate %[:string {:min 1 :max 5}] "asdfghjk") ;=> false
  (%[:string {:min 1 :max 5}]? "asdfghjk") ;=> false
  
  ; now allow me to use validate to introduce any new function to you
  (validate [:=> [:cat [:as :a type] [:ref :a]] provide) ; provide a random value of this type
  ; note here it's actually a dependently-typed function, input is a type and output is a value of that type.
  ; I don't find a good syntax for it right now but it's clear the idea of it
  
  ; bindings with slotted type
  (def t %[:cat [:string 'a] [:int 'b]])
  (let [{:a aval :b bval} :from t '("a" 1)] (prn aval) (prn bval))
  ```
- It's clear that Rich Hickey is escaping from types all the time, but in his language types get reinvented. So what I want to do is bring them back in the next generation, in a gradual way.