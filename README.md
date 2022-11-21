# [Snowpipe theATeam Unity C# Style Guide]

* 우리의 가장 중요한 목적은 **간결성**, **가독성**, 그리고 **단순함** 이다.
* 우리의 스타일 만드는데 다음을 참고한다.
 1. Unity Scripting Reference
 2. MS C# Coding Conventions
 3. 내부 합의

## Table of Contents

- [Language](#language)
- [Project Structure](#project-structure)
- [File Naming](#file-naming)
  + [Unity Scripts Naming](#unity-scripts-naming)
- [Variables & Functions Orders](#variables--functions-orders)
- [Cases](#cases)
- [Nomenclature](#nomenclature)
  + [Namespaces](#namespaces)
  + [Classes & Interfaces](#classes--interfaces)
  + [Methods](#methods)
  + [Variables](#variables)
  + [Constants](#constants)
  + [Fields](#fields)
  + [Properties](#properties)
  + [Parameters](#parameters)
  + [Misc](#misc)
- [Declarations](#declarations)
  + [Access Level Modifiers](#access-level-modifiers)
  + [Fields & Variables](#fields--variables)
  + [Classes](#classes)
  + [Interfaces](#interfaces)
- [Spacing](#spacing)
  + [Indentation](#indentation)
  + [Line Length](#line-length)
  + [Vertical Spacing](#vertical-spacing)
- [Brace Style](#brace-style)
- [Switch Statements](#switch-statements)
- [File Naming](#file-naming)
- [Unity C# Naming](#unity-c-naming)
- [Copyright Statement](#copyright-statement)


## Language

미국 영어 철자를 사용한다.

**AVOID:**

```csharp
string colour = "red";
```

**PREFER:**

```csharp
string color = "red";
```

## Project Structure
<pre>
Assets
    <a name="#structure-developers">_Developers</a>(Use a `_`to keep this folder at the top)
        DeveloperName
            (Work in progress assets)
    <a name="structure-top-level">ProjectName</a>
        Animations       Animation clips
        Editor           Editor specified scripts, prefabs, etc
        Fonts            Fonts used in game
        Materials        Texture materials
        Prefabs          In-game prefabs
        Resources        Unity resources assets
        Scenes           Scenes
        Scripts          Code scripts, grouped in sub-folders
        Sounds           Musics, sounds
        Textures         Image textures
          UI             Texture used for UI
          Icons          App icons
        Plugins
</pre>

## File Naming
image를 제외한 모든 파일은 *PascalCase*를 따른다.

* Folders - PascalCase
  FolderName/
* Images - 모두 소문자이며 중간에 -(hyphen) 추가한다.
  image-name-64x64.jpg
* The rest - PascalCase
  ScriptName.cs, PrefabName.prefab, SceneName.unity
  
### Unity Scripts Naming
각각 스크립트는 개별의 목적을 가지고 있다. 간단히 파일명으로 스크립트가 무엇을 하는지 유추할 수 있도록 다음의 규칙을 따른다.

* XxxPanel, XxxSlot, XxxButton, etc for UI
  * MenuPanel, AchievementSlot, CoinShopButton

* XxxManager, for master scripts that control specific workflow 씬에서 인스턴스 하나만 존재한다.)
  * DailyMissionManager, AchievementManager

* XxxController, for scripts controlling a game object (씬에서 하나이상의 인스턴스가 존재할 수 있다.)
  * PlayerController, BossController, BackgroundController

* XxxDatabase, for a database (e.g CSV) which contains a list of data rows
  * WeaponDatabase, CardDatabase
  
* XxxData, for data row in a CSV database
  * WeaponData, CardData
  
* XxxItem, for in-game item instance
  * CardItem, CharacterItem

* XxxGenerator, for scripts instantiate GameObjects
  * ObjectGenerator, LandGenerator

* XxxSettings, for settings scripts inherent **Unity's ScriptableObject class**
  * AchievementSettings, DailyLoginSettings

* XxxEditor, for **editor-only scripts inherent Unity's Editor class**
  * TutorialTaskEditor, AchievementSettingsEditor

Manager와 Controller의 차이점은 Manager는 싱글턴이거나 Static이며, 여러 Object, asset들이 관여된 특수한 게임 로직을 관리한다. 반면에 Controller는 Object만을 관리하며, 여러개의 인스턴스가 만들어질 수도 있다. 예를 들어 Scene에서 여러개의 EnemyController는 각각의 enemy만을 관리한다.

Data와 Item의 차이점 : Item은 게임 내에서 하나의 인스턴스이며, 대부분의 경우 Data를 포함하고 있다. 예를 들어, CardData는 한 card의 미리 세팅된 모든 속성들을 가지고 있는 반면에, CardItem은 CardData 뿐만 아니라 개별 플레이어들의 다양한 (카드 레벨 같은) 속성들도 가지고 있다. 

## Variables & Functions Orders
```csharp
public class MyClass : MonoBehaviour
{
    // Constant variables
    public const int CONST_1;
    private const string CONST_2;

    // Static variables
    public static int s_static1;
    private static string s_static2;
    
    // Editor-assigned variables
    [SerializeField] 
    private Image maskImage;  
    [SerializeField] 
    private MyClass anotherComponent;

    // Other varaibles
    public int PrimitiveVariables1;
    private string _primitiveVariable2;

    // Properties
    public int Property1 
    {
      get; set;
    }
    
    private string property2 
    {
      get; set;
    }

    // Unity functions
    Awake()
    OnEnable()
    Start()
    Update()
    FixedUpdate()

    // Other custom functions
    Init()
    ...
    Reset()

    // Unity functions
    OnDisable()
    OnDestroy()

#region UNITY_EDITOR
    // Eebug functions that only runs in Editor
    [ContextMenu("...")]
    private void DebugFunction1()
    [MenuItem("Debug/...")]
    private static void DebugFunction2()
#endregion
}
```

긴 스크립트에서 #region을 사용하여 Group을 만들 수 있다.
(30 라인 내외의 작은 Script일 경우만, 그 이상이면 다른 Script로 분리하자.)

```
#region Timer
    private const int RESET_SECONDS = 180;
    private float _secondsLeft;
    public float CanResetTime => _secondsLeft < 0;
    public void ResetTime()
    {
      _secondsLeft = RESET_SECONDS;
    }
#endregion Timer
... // Other regions
```

## Cases
이름을 지정할 수 있는 여러가지 방법이 있는데, 몇가지를 소개한다.

> ##### PascalCase
> 각 단어의 첫 글자를 대문자로 표시하고 모든 공백삭제, e.g. `DesertEagle`, `StyleGuide`, `ASeriesOfWords`.
> 
> ##### camelCase
> 첫글자는 항상 소문자이지만 그 이후의 단어들은 대문자로 시작, e.g. `desertEagle`, `styleGuide`, `aSeriesOfWords`.
>
> ##### lowercase
> 모든 글자가 소문자, e.g. `deserteagle`, 
>
> ##### Snake_case
> 단어들은 임의대로 대문자나 소문자로 시작하나 단어들은 underscore에 의해 분리, e.g. `desert_Eagle`, `Style_Guide`, `a_Series_of_Words`.

## Nomenclature

전체적으로 네이밍은 C# 표준을 따른다.

### Namespaces

네임스페이스는 모두 **PascalCase**를 따른다. 하이픈( - ) 또는 언더스코어( \_ )를 사용하지 않는다. 이 룰의 예외는 GUI 나 HUD 같은 대문자인 축약어이다.

**AVOID**:

```csharp
com.raywenderlich.fpsgame.hud.healthbar
```

**PREFER**:

```csharp
RayWenderlich.FPSGame.HUD.Healthbar
```

### Classes & Interfaces

클래스와 인터페이스는 **PascalCase**를 따른다. 예) `RadialSlider`

### Methods

메소드는 **PascalCase**를 따른다. 동사로 시작하며 필요하다면 그 뒤에 명사를 붙이서 만든다. 예) `DoSomething()`. `Reward()`, `StartGame()`.

### Variables

기본적으로 대부분의 변수들은 **camelCase**를 사용하며, 항상 명사 또는 형용사의 형태이다. 예) `hasRewarded`, `currentPosition`, `isInitialized`.

### Constants

모든 상수들은 대문자와 언더스코어(\_)로 표현한다. 예) `SHOW_SUB_MENU_LEVEL`, `MAX_HP_COUNT`, `BASE_DAMAGE`.

### Fields

static이 아닌 모든 필드들은 camelCase를 따른다. Unity 컨벤션에 따르면 **public 필드**도 역시 포함된다.

예)

```csharp
public class MyClass 
{
    public int publicField;
    protected int myProtected;    
    private int packagePrivate;
    private int myPrivate;
}
```

**AVOID:**

```csharp
private int _myPrivateVariable
```

**PREFER:**

```csharp
private int myPrivateVariable
```

Static 필드들은 예외적으로 **PascalCase**를 사용한다:

```csharp
public static int TheAnswer = 42;
```

메소드에 파라메터가 없다면, **PascalCase**로 멤버 프로퍼티를 사용한다.

**AVOID:**

```csharp
bool IsRewarded() { }
```

**PREFER:**

```csharp
bool IsRewarded 
{
     get 
     { 
         return …
     } 
}
```
또는 짧게 다음과 같이 사용할 수도 있다.

```csharp
bool IsRewarded => ...;
```

### Properties

모든 Property들은 **PascalCase**를 사용한다.
예)

```csharp
public int PageNumber 
{
    get 
    { 
        return pageNumber; 
    }

    set 
    { 
        pageNumber = value; 
    }
}
```

내부적으로 값을 쓰고 외부에 액세스할 수 있게 사용할 수도 있다. 
```csharp
public int Id 
{ 
    get; private set; 
}

public Sprite IconSprite 
{ 
    get; private set; 
}
```

### Parameters

파라메터는 **camelCase** 를 사용한다.

**AVOID:**

```csharp
void DoSomething(Vector3 Location)
```

**PREFER:**

```csharp
void DoSomething(Vector3 location)
```

한문자 변수는 임시 반복문이 아닌 이상 사용하지 않도록 한다.

### Actions/Callback Event
액션은 **PascalCase**을 사용한다. 명사와 수동태를 조합하여 사용한다.
예)

```csharp
public event Action<int> ValueChanged;
public static event UnityAction GameStarted;
public static UnityAction CharacterDied;
```

### Misc

코드에서 축약어는 단어와 동일하게 다룬다.(모든 글자를 대문자로 쓰지 않는다는 의미)
예)

**AVOID:**

```csharp
XMLHTTPRequest
String URL
findPostByID
```  

**PREFER:**

```csharp
XmlHttpRequest
String url
findPostById
```

## Declarations

### Access Level Modifiers

접근제한자는 클래스, 메소드, 필드에 대해 명시적으로 표시한다.

### Fields & Variables

필드나 변수 선언시 한 라인에 하나씩 선언한다.

**AVOID:**

```csharp
string username, twitterHandle;
```

**PREFER:**

```csharp
string username;
string twitterHandle;
```

### Classes

정확히 소스 파일 하나당 하나의 클래스만 사용한다. 적절한 범위라면 inner 클래스는 허용한다.

### Interfaces

모든 인터페이스는 **I** 대문자를 첫글자로 사용한다.

**AVOID:**

```csharp
RadialSlider
```

**PREFER:**

```csharp
IRadialSlider
```

## Spacing

### Indentation

들여쓰기는 **space**를 사용하며, TAB은 사용하지 않는다.

#### Blocks

블럭을 위한 들여쓰기는 **4 spaces**를 사용한다.

**AVOID:**

```csharp
for (int i = 0; i < 10; i++) 
{
  Debug.Log("index=" + i);
}
```

**PREFER:**

```csharp
for (int i = 0; i < 10; i++) 
{
    Debug.Log("index=" + i);
}
```

#### Line Wraps

자동 줄 바꿈의 경우는 **4 spaces** 사용한다.

**AVOID:**

```csharp
CoolUiWidget widget =
        someIncrediblyLongExpression(that, reallyWouldNotFit, on, aSingle, line);
```

**PREFER:**

```csharp
CoolUiWidget widget =
    someIncrediblyLongExpression(that, reallyWouldNotFit, on, aSingle, line);
```

### Line Length

한 라인은 **100* 글자를 넘지 않도록 한다.

### Vertical Spacing

시각적인 명확함과 조직화를 위해 메소드간에는 정확히 한 라인을 비우도록 한다. 
메소드 안에서 화이트스페이스는 기능적으로 분리할 때 사용하며, 
한 메소드안에 너무 많은 섹션이 있다는 의미는 여러개의 메소드로 나눠야한다는 것을 의미한다.

## Brace Style

모든 중괄호는 C# 컨벤션과 동일하게 한라인에 놓는다.

**AVOID:**

```csharp
class MyClass {
    void DoSomething() {
        if (someTest) {
          // ...
        } else {
          // ...
        }
    }
}
```

**PREFER:**

```csharp
class MyClass
{
    void DoSomething()
    {
        if (someTest)
        {
          // ...
        }
        else
        {
          // ...
        }
    }
}
```

조건문은 필요한 라인 수를 무시하고 항상 중괄호로 둘러싸도록 한다. 

**AVOID:**

```csharp
if (someTest)
    doSomething();  

if (someTest) doSomethingElse();
```

**PREFER:**

```csharp
if (someTest) 
{
    DoSomething();
}  

if (someTest)
{
    DoSomethingElse();
}
```

## Switch Statements

스위치문의 `default` case가 절대 호출되지 않으면 확실히 삭제한다.

**AVOID:**  
  
```csharp
switch (variable) 
{
    case 1:
        break;
    case 2:
        break;
    default:
        break;
}
```

**PREFER:**  
  
```csharp
switch (variable) 
{
    case 1:
        break;
    case 2:
        break;
}
```

## Unity C# Naming
### Variable Naming
All capital letters and PREFS_XXXX for PlayerPref keys
```csharp 
   private const string PREFS_LAST_FREE_DRAW = "LastFreeDraw";
   PlayerPrefs.GetInt(PREFS_LAST_FREE_DRAW)
```   

Use xxxGO for scene GameObject variables. 예)`optionButtonGO`, `backgroundMaskGO`
   
Use xxxPrefab for scene GameObject variables. 예)`weaponSlotPrefab`, `explosionPrefab`

Use xxxTransform for transform variables. 예) `weaponTransform`, `armTransform`
   
Use xxxComponent for all other components.

그 외에 직관적으로 알 수 있는 이름들. 예) `eyesSpriteRenderer`, `runAnimation`, `attackAnimationClip`, `victoryAudioClip`
   
Use xxxxs for arrays
```csharp
   slotPrefabs = new Prefabs[0];
   achievementIds = new int[0];
```   

Use xxxxList for List and xxxxDict for Dictionary
```csharp
   weaponTransformList = new List();
   achievementProgressDict = new Dictionary();
```

### Actions/Callback Event
액션은 **PascalCase**을 사용한다. 명사와 수동태를 조합하여 사용한다.
예)

```csharp
public event Action<int> OnValueChanged;
public static event UnityAction OnGameStarted;
public static UnityAction OnCharacterDied;
```

### Functions Naming
Use OnXxxxClick, OnXxxxTouch, for UI button clicks or touch.
```csharp
public void OnStartClick();
public void OnCancelTouch();
```

## Copyright Statement

The following copyright statement should be included at the top of every source file:

/*
* Copyright (C) 2021, Snowpipe
* All rights reserved.
*/
