# Framework guideline
The purpose of this document is to guide the developer code in the framework repo. We are going step by step at a creation of an object and explain each process.

## concrete object creation

Let's go step by step at creating an object called `Byte` that will inherit from [object](implementation/object.md). `Byte` is concrete object and it represent a `uint8_t` byte value. If you are creating an other module of you own, you can still follow along and apply the step for you module.

### step 1 generate a concrete object class

With start with the modgen.sh script to generate our code. We want a concrete object called byte and stored in `src/concrete/byte`
```
`$ ./modgen.sh concrete -d byte -m byte -s object`
```
The script will generate in total 3 files:
- `src/concrete/byte/Byte.c`
- `src/concrete/byte/Byte.h`
- `test/concrete/bytetest_Byte.c`


Let's make sure that everything is setup by executing the byte test code. We normaly have ceedling installed in our environement. If not ask a staff responsible for the framework maintenance.
Write the following command to execute the generated test for our module `Byte`.
```
$ ceedling test:_Byte
```
The console should print the following:
```
--------------------
OVERALL TEST SUMMARY
--------------------
TESTED:  3
PASSED:  3
FAILED:  0
IGNORED: 0

```
If it doesnt, it means that a breaking change was introduce since, ask the a framework assistant for help.

### step 2 The test file

Let's take a look at the test file first lines of code.
```cs
static pObject_t _object = NULL;
void setUp(void)
{
    _object = Byte_Create();
}

void tearDown(void)
{
    Object_Destroy(_object);
    Support_Assert_Reset();
}

```
We have a `setUp` and a `TearDown` that are available. An object is already declared as a global variable. setUP will call `Byte_Create` to have an object byte instance. The tearDown will simply call destroy to de-initialise our object.

The rest of the code contains **3 tests** related to the object creation and destruction and **1 test** that serves as an example guideline to writing tests. You can notice that one test function is commented out.

```cs
// void test_Byte_Create_WhenCreateMultiple_ShouldBeDifferentMemoryAlloc(void)
// {
//     pObject_t a = Byte_Create();
//     pObject_t b = Byte_Create();

//     TEST_ASSERT_NOT_EQUAL(a, b);

//     Object_Destroy(a);
//     Object_Destroy(b);
// }
```
If we want our module to be multiple instance we should enable this test by **de-commenting** it. Let's do it and run the tests again!

```
--------------------
OVERALL TEST SUMMARY
--------------------
TESTED:  4
PASSED:  4
FAILED:  0
IGNORED: 0

```
If the test pass (in our case yes). It means the code generator has assumed more than one instance for our module and has generated an entry in [test/support/Config.h](../test/support/Config.h). If we wish to have less or more object or maybe have the max instance private, feel free to change the value for your module.

### step 3 The source code
Let's take a look at the source code generated and see what is all this about. `Byte.h` only contain 1 declaration `pObject_t  Byte_Create(void);`. `Byte.c` is more interesting. First we have the private formward declaration for our type, followed by a definition to `self`
```cs
typedef struct byte_s* pByte_t;
#define self        ((pByte_t)super)
```

`self` is use as a keyword for the concrete object. Method will always have a `super` parameter designation the `self`. From the user perspective, he is using an abstract object and not a concrete byte object. **Do not move the self definition in the header file**. On the other hand the forward declaration can be in the header file. If we wish to extend the functionality of object and have new feature then declare the type in the header file. `byte_s` should be **encapsulated in the source file and not expose**.


Next, we have the define for the `BYTE_MAX_COUNT` which is the total number of object byte we can instanciate. 
```cs
#ifndef BYTE_MAX_COUNT
    #define BYTE_MAX_COUNT 1
    #warning "BYTE_MAX_COUNT is undefined set default to 1"
#endif // BYTE_MAX_COUNT

```
the config file is already included and there is already an entry for `BYTE_MAX_COUNT` so we shouldn't warning. We can see that through out the file, the generator has left indication on where to write our code.
```cs
/// USER CODE
..
/// USE CODE END
```

The generator script already expected us to inherit from object and propose us to indicate how to destroy our object. `Byte_Create` function is responsible for creating a new instance of `Byte` and initialise it's private attributes. We can see a function call `Object_Constructor2`. This function is a macro that create an object instance using the [object pool](implementation/object.md) and will declare an instance `super`. The macro take as parameters:
- the max capacity instance, 
- the object name 
- and the interface for object.
 
The generator assumes that we want to re-implement the object methods but the object methods have default implementation so we can use `Object_Constructor1` if we don't need to implement anything about object. It's not the case for our example `Byte`. 


### step 4 Start developing

You probably have a good idea of what are the requirement for your module. Before we start coding we need a test plan. Write down what test you plan to write to fullfil the requirements. 
- Byte requirement:
  - Byte should store a `uint8_t`  value
  - We can copy a byte and create a new byte with a copied value
  - We can compare bytes
  - We can add byte
  - Should be able to get the difference of byte

Our test plan for byte would be the following:
- When Create should set an initial value
- When Copy the new byte should have the same value
- When Diff byte value should decrease
- Can create multiple byte
- When Diff should return the difference of value
- Add byte toghther should increase value by other
- ...

Once we have our plan for the test we can start writing. And by writing we mean to follow [TDD](test-driven.md)! As the Test plan implies, we want to create a byte object with an initial value. That  changes the genreate `Byte_Create` because we need to feed it with a `uint8_t` initial value.Let's change all the Create function to fullfill that requirement.

```cs
Byte_Create(1);
```

We run the test and we expect a failure!

```
test/byte/concrete/test_Byte.c: In function 'setUp':
test/byte/concrete/test_Byte.c:62:15: error: too many arguments to function 'Byte_Create'
     _object = Byte_Create(1);
...
...

Tasks: TOP => build/test/out/c/test_Byte.o
(See full trace by running task with --trace)
ERROR: Ceedling Failed
```
We can now make the change to the source code
```cs
//in the header file
pObject_t Byte_Create(uint8_t initialValue);
//in the source file
pObject_t Byte_Create(uint8_t initialValue)
{
    ...
```
Let's run the test again.
```
$ ceedling test:_Byte
```
And we see that everything work. Remember to do the absolute minimum to have a test pass.

Now let's implement the byte equal method. We first write the test!
> you can get rid of the `test_Byte_Function_WhenSomething_ShouldDoSomething` test

Let's first make a test that compare 2 different objects.
```cs
void test_Byte_IsEqual_WhenObjectAreDifferent_ShouldBeReturnFalse(void)
{
    pObject_t a = Byte_Create(1);
    pObject_t b = Byte_Create(2);
    
    TEST_ASSERT_EQUAL(false, Object_IsEqual(a, b));
}
```
We should have a compilation error and expect it. **No?** What is happening? It is important to first state that we should always have a failing test before fixing it. If the test pass, then we should investigate why and fix it. We can see that if we navigate in the [object source code](../src/object/abstract/Object.c), there is a default implementation for `IsEqual`. It compare the object id! **That why the test pass**. Before doing any changes to `Byte` source code, let's change the test to check for the **same object** instead.

```cs
void test_Byte_IsEqual_WhenObjectAreSame_ShouldBeReturnTrue(void)
{
    pObject_t a = Byte_Create(1);
    pObject_t b = Byte_Create(1);
    
    TEST_ASSERT_EQUAL(false, Object_IsEqual(a, b));
}
```
Now we have our failing test!

```
-------------------
FAILED TEST SUMMARY
-------------------
[test_Byte.c]
  Test: test_Byte_IsEqual_WhenIsEqualObject_ShouldBeReturnTrue
  At line (119): "Expected 1 Was 0"

--------------------
OVERALL TEST SUMMARY
--------------------
TESTED:  4
PASSED:  3
FAILED:  1
IGNORED: 0

---------------------
BUILD FAILURE SUMMARY
---------------------
Unit test failures.

```
We can now fix this test and look at how we re-implement methods.
#### Reimplementing methods

implementing object method is extremely easy.
1. We declare the function method
   ```cs
   static bool _IsEqual(pObject_t, pObject_t);
   ```
2. We assign the method to the interface object
    ```cs
    static objectInterface_t _interfaceObj = 
    {
        .Destroy = _Destroy,
        .IsEqual = _IsEqual
    };
    ```
3. Adn finally we implement the method body
   ```cs
    static bool _IsEqual(pObject_t super, pObject_t other)
    {

    }
    ```
We don't need to go further, first make sure that everything compiles. It important to have an incremental strategy to our development so we can easily pin point were the issue happens. So Let's run the test and see that it still compile and the test still fail.
```
[test_Byte.c]
  Test: test_Byte_IsEqual_WhenObjectAreSame_ShouldBeReturnTrue
  At line (119): "Expected 1 Was 44"

```
This time we get a different outcome, but still a failing test, **which is good!**
We make it pass with the least possible effort.
```cs
static bool _IsEqual(pObject_t super, pObject_t other)
{
    return true;
}
```

Now the test pass! What do we do next? 
The next step is to write another failing test that we can think of... Any idea?
We can simply write the same test but this time with different object so we expect a false return.
```cs
void test_Byte_IsEqual_WhenObjectAreDifferent_ShouldBeReturnFalse(void)
{
    pObject_t a = Byte_Create(1);
    pObject_t b = Byte_Create(2);
    
    TEST_ASSERT_EQUAL(false, Object_IsEqual(a, b));
}
```

the test fail as expect, it's because we hack it, which is a normal process in TDD. We have no choice now but to make the effort and actually implement the correct behavior. The test have driven us toward this.

We add a attribute to our object byte

```cs
typedef struct byte_s
{
    object_t  super;//inheritance should always be first in the struct
    /// USER CODE BEGIN
    //  class attribute 
    uint8_t value;
    /// USER CODE END
} byte_t;
```
Then we set it initial value at the create.

```cs
pObject_t Byte_Create(uint8_t initialValue)
{
    Object_Constructor2(BYTE_MAX_COUNT, byte, _interfaceObj);
    ASSERT_DEBUG(super);

    // USER CODE BEGIN
    // Write the Byte initialisation process
    self->value = initialValue;
    // USER CODE END

    return ObjectCast(super);

}


```
And finally we do our comparaison correctely.
```cs
static bool _IsEqual(pObject_t super, pObject_t superOther)
{
    pByte_t other = (pByte_t)superOther;
    return (other->value == self->value);
}
```
And the test pass! as expected.

```
--------------------
OVERALL TEST SUMMARY
--------------------
TESTED:  5
PASSED:  5
FAILED:  0
IGNORED: 0
```


