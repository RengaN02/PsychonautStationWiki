# Stil Rehberi
Kodun çoğunluğu bu kurallara uymuyor olabilir ama bu bizim uymayacağımız anlamına gelmez

1. [Genel](#genel)
2. [Paths and Inheritence](#paths-and-inheritence)
3. [Variables](#variables)
4. [Procs](#procs)
5. [Macros](#macros)
6. [Things that do not matter](#things-that-do-not-matter)

## Genel

### Tab kullan, boşluk değil.
Satır başlarında boşluk değil tab kullan

Bir kod satırının ortasında tab kullanmayın. Bu sadece bir sekmenin boyutu tanımsız olduğu için tutarsız olmakla kalmaz, aynı zamanda hizaladığınız satırın boyutu değişirse, bir ton başka kodu ayarlamamız gerektiği anlamına gelir. Ayrıca, çoğu zaman okunabilirliğe zarar verir.

```dm
// Kötü
#define SPECIES_MOTH			"moth"
#define SPECIES_LIZARDMAN		"lizardman"
#define SPECIES_FELINID			"felinid"

// İyi
#define SPECIES_MOTH "moth"
#define SPECIES_LIZARDMAN "lizardman"
#define SPECIES_FELINID "felinid"
```

### Kontrol Yapıları
(if, while, for, vs)

* Kontrol kullandığın satırda bir kod çalıştırma.
```dm
// Kötü
/proc/yap(x)
	if(x > 2) return

// İyi
/proc/yap(x)
	if(x > 2)
		return
```
* All control statements comparing a variable to a number should use the formula of `thing` `operator` `number`, not the reverse (eg: `if (count <= 10)` not `if (10 >= count)`)

### Operatör Kullanımı
* Bitwise '&' kullanımı
	* `variable & CONSTANT` şeklinde yazılmalı, `CONSTANT & variable` değil. İkiside çalışır fakat 2.si kafa karıştırıyor.
 
### Globalyerine statik kullan.
DM'nin global adı verilen bir var anahtar sözcüğü vardır. Bu var anahtar sözcüğü türlerin içindeki değişkenler içindir. Örneğin:

```dm
/mob
	var/global/thing = TRUE
```
Bu, ona global bir değişken gibi her yerden erişebileceğiniz anlamına gelmez. Bunun yerine, var'ın türünün tüm örnekleri için yalnızca bir kez var olacağı anlamına gelir; bu durumda var, tüm moblar için yalnızca bir kez var olacaktır - türündeki her şeyde paylaşılır. (PHP/C++/C#/Java gibi diğer dillerdeki 'statik' anahtar kelimesine çok benzer) 

Bu kafa karıştırıcı değil mi? 

Ayrıca, global ile aynı davranışa sahip olan ancak BYOND'un davranışını daha doğru şekilde tanımlayan, 'statik' adı verilen belgelenmemiş bir anahtar kelime de vardır. Bu nedenle, BYOND kodunu okurken sürprizi azalttığı için ihtiyaç duyduğumuzda global yerine her zaman statik kullanıyoruz.

### Erken return kullanımı

Bu kötü:
````DM
/datum/datum1/proc/proc1()
	if (thing1)
		if (!thing2)
			if (thing3 == 30)
				do stuff
````
Bu güzel:
````DM
/datum/datum1/proc/proc1()
	if (!thing1)
		return
	if (thing2)
		return
	if (thing3 != 30)
		return
	do stuff
````

### Ne olduğu belirsiz sihirli sayılar veya sözcükler kullanma.

Bir değişkeni sayı ile belirtmek yerine onun için bir tanım oluştur ardından onu kullan.

Bu kötü: 
````dm
/datum/proc/do_the_thing(thing_to_do)
	switch(thing_to_do)
		if(1)
			(...)
		if(2)
			(...)
````
Burada 1 ve 2 belirsiz, 1 ne? 2 ne? Onun yerine bu şekil yap:

````dm
#define DO_THE_THING_REALLY_HARD 1
#define DO_THE_THING_EFFICIENTLY 2
/datum/proc/do_the_thing(thing_to_do)
	switch(thing_to_do)
		if(DO_THE_THING_REALLY_HARD)
			(...)
		if(DO_THE_THING_EFFICIENTLY)
			(...)
````

Bu şekilde kod daha temiz ve okunabilir oldu.

### Zaman tanımlarını kullan

Tanımlar şu şekildedir:
* SECONDS
* MINUTES
* HOURS

Bu kötü:
````DM
/datum/datum1/proc/proc1()
	if(do_after(mob, 15))
		mob.dothing()
````

Bu iyi:
````DM
/datum/datum1/proc/proc1()
	if(do_after(mob, 1.5 SECONDS))
		mob.dothing()
````

## Paths and Inheritence

### Type paths must begin with a `/`
eg: `/datum/thing`, not `datum/thing`

### Type paths must be snake case
eg: `/datum/blue_bird`, not `/datum/BLUEBIRD` or `/datum/BlueBird` or `/datum/Bluebird` or `/datum/blueBird`

### Datum type paths must began with "datum"
In DM, this is optional, but omitting it makes finding definitions harder.

## Variables

### Use `var/name` format when declaring variables
While DM allows other ways of declaring variables, this one should be used for consistency.

### Use descriptive and obvious names
Optimize for readability, not writability. While it is certainly easier to write `M` than `victim`, it will cause issues down the line for other developers to figure out what exactly your code is doing, even if you think the variable's purpose is obvious.

#### Any variable or argument that holds time and uses a unit of time other than decisecond must include the unit of time in the name.
For example, a proc argument named `delta_time` that marks the seconds between fires could confuse somebody who assumes it stores deciseconds. Naming it `delta_time_seconds` makes this clearer, naming it `seconds_per_tick` makes its purpose even clearer.

### Don't use abbreviations
Avoid variables like C, M, and H. Prefer names like "user", "victim", "weapon", etc.

```dm
// What is M? The user? The target?
// What is A? The target? The item?
/proc/use_item(mob/M, atom/A)

// Much better!
/proc/use_item(mob/user, atom/target)
```

Unless it is otherwise obvious, try to avoid just extending variables like "C" to "carbon"--this is slightly more helpful, but does not describe the *context* of the use of the variable.

### Naming things when typecasting
When typecasting, keep your names descriptive:
```dm
var/mob/living/living_target = target
var/mob/living/carbon/carbon_target = living_target
```

Of course, if you have a variable name that better describes the situation when typecasting, feel free to use it.

Note that it's okay, semantically, to use the same variable name as the type, e.g.:
```dm
var/atom/atom
var/client/client
var/mob/mob
```

Your editor may highlight the variable names, but BYOND, and we, accept these as variable names:

```dm
// This functions properly!
var/client/client = CLIENT_FROM_VAR(usr)
// vvv this may be highlighted, but it's fine!
client << browse(...)
```

### Name things as directly as possible
`was_called` is better than `has_been_called`. `notify` is better than `do_notification`.

### Avoid negative variable names
`is_flying` is better than `is_not_flying`. `late` is better than `not_on_time`.
This prevents double-negatives (such as `if (!is_not_flying)` which can make complex checks more difficult to parse.

### Exceptions to variable names

Exceptions can be made in the case of inheriting existing procs, as it makes it so you can use named parameters, but *new* variable names must follow these standards. It is also welcome, and encouraged, to refactor existing procs to use clearer variable names.

Naming numeral iterator variables `i` is also allowed, but do remember to [Avoid unnecessary type checks and obscuring nulls in lists](./STANDARDS.md#avoid-unnecessary-type-checks-and-obscuring-nulls-in-lists), and making more descriptive variables is always encouraged.

```dm
// Bad
for (var/datum/reagent/R as anything in reagents)

// Good
for (var/datum/reagent/deadly_reagent as anything in reagents)

// Allowed, but still has the potential to not be clear. What does `i` refer to?
for (var/i in 1 to 12)

// Better
for (var/month in 1 to 12)

// Bad, only use `i` for numeral loops
for (var/i in reagents)
```

### Don't abuse the increment/decrement operators
`x++` and `++x` both will increment x, but the former will return x *before* it was incremented, while the latter will return x *after* it was incremented. Great if you want to be clever, or if you were a C programmer in the 70s, but it hurts the readability of code to anyone who isn't familiar with this. The convenience is not nearly good enough to justify this burden.

```dm
// Bad
world.log << "You now have [++apples] apples."

// Good
apples++
// apples += 1 - Allowed
world.log << "You now have [apples] apples."

// Bad
world.log << "[apples--] apples left, taking one."

// Good
world.log << "[apples] apples left, taking one."
apples--
```

### initial() versus ::
`::` is a compile time scope operator which we use as an alternative to `initial()`.
It's used within the definition of a datum as opposed to `Initialize` or other procs.

```dm
// Bad
/atom/thing/better
	name = "Thing"

/atom/thing/better/Initialize()
	var/atom/thing/parent = /atom/thing
	desc = inital(parent)

// Good
/atom/thing/better
	name = "Thing"
	desc = /atom/thing::desc
```

Another good use for it easy access of the parent's variables.
```dm
/obj/item/fork/dangerous
	damage = parent_type::damage * 2
```

```dm
/obj/item/fork
	flags_1 = parent_type::flags_1 | FLAG_COOLER
```


It's important to note that `::` does not apply to every application of `initial()`.
Primarily in cases where the type you're using for the initial value is not static.

For example,
```dm
/proc/cmp_subsystem_init(datum/controller/subsystem/a, datum/controller/subsystem/b)
	return initial(b.init_order) - initial(a.init_order)
```
could not use `::` as the provided types are not static.

## Procs

### Getters and setters

* Avoid getter procs. They are useful tools in languages with that properly enforce variable privacy and encapsulation, but DM is not one of them. The upfront cost in proc overhead is met with no benefits, and it may tempt to develop worse code.

This is bad:
```DM
/datum/datum1/proc/simple_getter()
	return gotten_variable
```
Prefer to either access the variable directly or use a macro/define.


* Make usage of variables or traits, set up through condition setters, for a more maintainable alternative to compex and redefined getters.

These are bad:
```DM
/datum/datum1/proc/complex_getter()
	return condition ? VALUE_A : VALUE_B

/datum/datum1/child_datum/complex_getter()
	return condition ? VALUE_C : VALUE_D
```

This is good:
```DM
/datum/datum1
	var/getter_turned_into_variable

/datum/datum1/proc/set_condition(new_value)
	if(condition == new_value)
		return
	condition = new_value
	on_condition_change()

/datum/datum1/proc/on_condition_change()
	getter_turned_into_variable = condition ? VALUE_A : VALUE_B

/datum/datum1/child_datum/on_condition_change()
	getter_turned_into_variable = condition ? VALUE_C : VALUE_D
```

### When passing vars through New() or Initialize()'s arguments, use src.var
Using src.var + naming the arguments the same as the var is the most readable and intuitive way to pass arguments into a new instance's vars. The main benefit is that you do not need to give arguments odd names with prefixes and suffixes that are easily forgotten in `new()` when sending named args.

This is very bad:
```DM
/atom/thing
	var/is_red

/atom/thing/Initialize(mapload, enable_red)
	is_red = enable_red

/proc/make_red_thing()
	new /atom/thing(null, enable_red = TRUE)
```

Future coders using this code will have to remember two differently named variables which are near-synonyms of eachother. One of them is only used in Initialize for one line.

This is bad:
```DM
/atom/thing
	var/is_red

/atom/thing/Initialize(mapload, _is_red)
	is_red = _is_red

/proc/make_red_thing()
	new /atom/thing(null, _is_red = TRUE)
```

`_is_red` is being used to set `is_red` and yet means a random '_' needs to be appended to the front of the arg, same as all other args like this.

This is good:
```DM
/atom/thing
	var/is_red

/atom/thing/Initialize(mapload, is_red)
	src.is_red = is_red

/proc/make_red_thing()
	new /atom/thing(null, is_red = TRUE)
```

Setting `is_red` in args is simple, and directly names the variable the argument sets.

### Prefer named arguments when the meaning is not obvious.

Pop-quiz, what does this do?

```dm
give_pizza(TRUE, 2)
```

Well, obviously the `TRUE` makes the pizza hot, and `2` is the number of toppings. 

Code like this can be very difficult to read, especially since our LSP does not show argument names at this time. Because of this, you should prefer to use named arguments where the meaning is not otherwise obvious.

```dm
give_pizza(hot = TRUE, toppings = 2)
```

What is "obvious" is subjective--for instance, `give_pizza(PIZZA_HOT, toppings = 2)` is completely acceptable.

Other examples:

```dm
deal_damage(10) // Fine! The proc name makes it obvious `10` is the damage...at least it better be.
deal_damage(10, FIRE) // Also fine! `FIRE` makes it obvious the second parameter is damage type.
deal_damage(damage = 10) // Redundant, but not prohibited.

use_energy(30 JOULES) // Use energy in joules.
turn_on(30) // Not fine!
turn_on(power_usage = 30) // Fine!

set_invincible(FALSE) // Fine! Boolean parameters don't always need to be named. In this case, it is obvious what it means.
```
