# UNDER HEAVEN

<img src="doc/underheaven_title.png" width= "75%" height="75%">
<table style="width:80%">
  <tr>
    <td>制作期間</td> <td>2019.3 ~ 2020.2</td>
  </tr>

  <tr>
    <td>制作人数</td> <td>18人</td>
  </tr>
  
  <tr>
    <td>役職</td> <td>プログラマー</td>
  </tr>
  
  <tr>
    <td>ジャンル</td> <td>アクションRPG</td>
  </tr>
  
  <tr>
    <td>プラットフォーム</td> <td>PC</td>
  </tr>
  
  <tr>
    <td>使用言語</td> <td>Unity C#</td>
  </tr>
  
  <tr>
    <td>GitHub</td> <td>非公開</td>
  </tr>
  
  <tr>
    <td>動画</td> <td>準備中</td>
  </tr>
</table>


# 主な仕事
## フリーバトルシーン全般
<table style="width:80%">
  <tr>
    <td>制作期間</td> <td>4週間</td>
  </tr>

  <tr>
    <td>制作人数</td> <td>1人</td>
  </tr>
  
  <tr>
    <td>使用言語</td> <td>Unity C#</td>
  </tr>
</table>

<img src="doc/underheaven_free.png" width= "75%" height="75%">



### 押した時の番号を参照してEnemyを生成
<img src="doc/underheaven_freebattle.png" width= "75%" height="75%">

* ユーザーが操作していてストレスを感じないように上下キーのみだったところを左右キーでもカーソルを動くようにもしました

```csharp
    public void OnClickCreateEnemy()
    {
        // 敵の生成
        if (dataflag)
        {
            UpdateCreateEnemy();
        }

        // Enemyの削除
        if (dataflag == false)
        {
            foreach (var enemy in createEnemyList)
            {
                AIManager.DeleteEnemy(enemy);
            }

            // 敵を削除したらすべてのFlagをfalse
            for (int i = 0; i < maxCount; i++)
            {
                dataflag = false;
                dataNums = 0;
                enemyCount[i] = null;
            }
            createEnemyList.Clear();
        }
    }
```

### 敵の生成をし、カウントをする処理
<img src="doc/underheaven_free_zoko.png" width= "75%" height="75%">
<img src="doc/underheaven_free_create_zoko.png" width= "75%" height="75%">
<img src="doc/underheaven_free_create_zoko3.png" width= "75%" height="75%">

```csharp
    private void CreateEnemy()
    {
        data.CreateEnemyInit();
        createEnemyList.Add(AIManager.Instance.EnemyClasses[AIManager.Instance.EnemyClasses.Count - 1]);
        for (int i = 0; i < maxCount; i++)
        {
            if (enemyCount[i] == null)
            {
                enemyCount[i] = AIManager.Instance.EnemyClasses[AIManager.Instance.EnemyClasses.Count - 1];
                break;
            }
        }
    }
```

### 死んだ敵がいたら値を減らす処理
```csharp
    private void DeathEnemyCount()
    {
        for (int i = 0; i < maxCount; i++)
        {
            if (enemyCount[i] != null)
            {
                if (enemyCount[i].IsDeath)
                {
                    dataNums--;
                    enemyCount[i] = null;
                }
            }
        }
    }
```

### 一定間隔で敵の生成
```csharp
    private void UpdateCreateEnemy()
    {
        if (dataNums >= maxCount) return;
        interval += Time.unscaledDeltaTime;
        if (interval >= createInterval)
        {
            interval = 0f;
            CreateEnemy();
            dataNums++;
        }
    }
```
## Editor拡張
### Enemyの行動ルートの可視化

<table style="width:80%">
  <tr>
    <td>制作期間</td> <td>3週間</td>
  </tr>

  <tr>
    <td>制作人数</td> <td>1人</td>
  </tr>
  
  <tr>
    <td>使用言語</td> <td>Unity C#</td>
  </tr>
</table>

* シーンビューでも行動ルートを触れるように設計しました

```csharp
private void OnSceneGUI()
    {
        
        //routeを表示する
        serializedObject.Update();
        SerializedProperty enemyRoute = serializedObject.FindProperty("enemyRoute");
        Vector3 createPosition = enemyRoute.FindPropertyRelative("createPosition").vector3Value;
        if (Selection.transforms == null || Selection.transforms.Length == 0) { return; }
        enemyRoute.FindPropertyRelative("createPosition").vector3Value = PositionHandle(createPosition, HandleUtility.GetHandleSize(Vector3.zero) * 0.5f,Selection.transforms[0].position);
        int size = enemyRoute.FindPropertyRelative("routes.Array.size").intValue;
        SerializedProperty routeDatas = enemyRoute.FindPropertyRelative("routes");
        for (int i = 0; i < size; i++)
        {
            SerializedProperty point = routeDatas.GetArrayElementAtIndex(i);
            //ルートを編集するためのボタンを作成
            float boxSize = HandleUtility.GetHandleSize(buttonSize) * 0.5f;
            if (boxSize >= 1.5f)
            {                
                if(Handles.Button(point.vector3Value + Selection.transforms[0].position, Quaternion.identity, 1.5f,1.5f, Handles.CubeHandleCap))
                {
                    selectIndex = i;
                }
            }
            else if(Handles.Button(point.vector3Value + Selection.transforms[0].position, Quaternion.identity, boxSize, boxSize, Handles.CubeHandleCap))
            {
                selectIndex = i;
            }
        }

        if (selectIndex >= 0)
        {
            SerializedProperty point = routeDatas.GetArrayElementAtIndex(selectIndex);
            point.vector3Value = PositionHandle(point.vector3Value, HandleUtility.GetHandleSize(Vector3.zero) * 0.5f,Selection.transforms[0].position);
        }
        
        //移動経路の表示
        for (int i = 0; i < size; i++)
        {
            int goalIndex = i + 1;
            if (goalIndex == size)
            {
                goalIndex = 0;
            }
            Vector3 startPosition = routeDatas.GetArrayElementAtIndex(i).vector3Value + Selection.transforms[0].position;
            Vector3 goalPosition = routeDatas.GetArrayElementAtIndex(goalIndex).vector3Value + Selection.transforms[0].position;
            Handles.DrawLine(startPosition,goalPosition);
        }
        serializedObject.ApplyModifiedProperties();
    }
    
    private Vector3 PositionHandle(Vector3 transform, float size, Vector3 offset)
    {
        var position = offset + transform;

        //X 軸
        Handles.color = Handles.xAxisColor;
        position = Handles.Slider(position, Vector3.right, size, Handles.ArrowHandleCap, 0.2f);

        //Y 軸
        Handles.color = Handles.yAxisColor;
        position = Handles.Slider(position, Vector3.up, size, Handles.ArrowHandleCap, 0.2f);

        //Z 軸
        Handles.color = Handles.zAxisColor;
        position = Handles.Slider(position, Vector3.forward, size, Handles.ArrowHandleCap, 0.2f);
        return position - offset;
    }
```
