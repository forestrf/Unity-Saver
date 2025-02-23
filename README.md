# Unity-Saver
![npm](https://img.shields.io/npm/v/extensions.unity.saver) [![openupm](https://img.shields.io/npm/v/extensions.unity.saver?label=openupm&registry_uri=https://package.openupm.com)](https://openupm.com/packages/extensions.unity.saver/) ![License](https://img.shields.io/github/license/IvanMurzak/Unity-Saver) [![Stand With Ukraine](https://raw.githubusercontent.com/vshymanskyy/StandWithUkraine/main/badges/StandWithUkraine.svg)](https://stand-with-ukraine.pp.ua)

Encrypted local data storage in Unity runtime on any platform. Thread safe, Unity-Saver uses dedicated non-main thread for Read/Write operations with files.
Data can be stored in different locations:
- :white_check_mark: Persistant - Regular Unity PersistantDataStorage location
  - ✔️ iOS
  - ✔️ Android
  - ✔️ Standalone
- :white_check_mark: Local - current location of executed app 
  - :x: iOS
  - :x: Android
  - ✔️ Standalone
- :white_check_mark: Custom - any path on your own
  - :x: iOS
  - :x: Android
  - ✔️ Standalone

![Unity Saver Settings](https://imgur.com/0RQeUQg.gif)

# How to install - Option 1 (RECOMMENDED)
- Install [ODIN Inspector](https://odininspector.com/)
- [Install OpenUPM-CLI](https://github.com/openupm/openupm-cli#installation)
- Open command line in Unity project folder
- `openupm add extensions.unity.saver`

# How to install - Option 2
- Install [ODIN Inspector](https://odininspector.com/)
- Add this code to <code>/Packages/manifest.json</code>
```json
{
  "dependencies": {
    "extensions.unity.saver": "1.0.8",
  },
  "scopedRegistries": [
    {
      "name": "package.openupm.com",
      "url": "https://package.openupm.com",
      "scopes": [
        "extensions.unity.saver",
        "com.cysharp",
        "com.neuecc"
      ]
    }
  ]
}
```

# Usage API
Usage is very simple, you have access to a data through <code>Data</code> property in saver class.
- <code>Data</code> - current data with read & write permission (does not read and write from file)
- <code>DefaultData</code> - access to default data, this data whould be taken on Load operation, only if file is missed or currupted
- <code>Load()</code> - force to execute load from a file operation, returns <code>async Task</code>
- <code>Save()</code> - insta save current <code>Data</code> to a file, returns <code>async Task</code>
- <code>SaveDelay()</code> - put save operation in a queue, if the same file is going to be saved twice, just single call will be executed. Very useful when data changes very often and required to call Save often as well. You may call SaveDelay as many times as need and do not worry about wasting CPU resources for continuously writing data into file.

# How to use in MonoBehaviour
```C#
using Extensions.Saver;

public class TestMonoBehaviourSaver : SaverMonoBehaviour<TestData>
{
    protected override string SaverPath => "TestDatabase";
    protected override string SaverFileName => "testMonoBehaviour.data";
}
```

# How to use in ScriptableObject
```C#
using UnityEngine;
using Extensions.Saver;

[CreateAssetMenu(menuName = "Example (Saver)/Test Scriptable Object Saver", fileName = "Test Scriptable Object Saver", order = 0)]
public class TestScriptableObjectSaver : SaverScriptableObject<TestData>
{
    protected override string SaverPath => "TestDatabase";
    protected override string SaverFileName => "testScriptableObject.data";

    protected override void OnDataLoaded(TestData data)
    {
        
    }
}
```

# How to use in C# class
```C#
using System;
using System.Threading.Tasks;
using Extensions.Saver;

public class TestClassSaver
{
    public Saver<TestData> saver;

    // Should be called from main thread, in Awake or Start method for example
    public void Init()
    {
        saver = new Saver<TestData>("TestDatabase", "testClass.data", new TestData());
    }

    public TestData Load() => saver?.Load();

    public async Task Save(Action onComplete = null) => await saver?.Save(onComplete);
}
```
