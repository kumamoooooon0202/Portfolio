# 水墨鳥獣

<img src="doc/rabbitfrog.png" width= "75%" height="75%">
<table style="width:80%">
  <tr>
    <td>制作期間</td> <td>2020.5 ~ 2021.2</td>
  </tr>

  <tr>
    <td>制作人数</td> <td>9人</td>
  </tr>
  
  <tr>
    <td>役職</td> <td bgcolor=cyan>プログラマーリーダー</td>
  </tr>
  
  <tr>
    <td>ジャンル</td> <td>タワーディフェンス</td>
  </tr>
  
  <tr>
    <td>プラットフォーム</td> <td>ios</td>
  </tr>
  
  <tr>
    <td>使用言語</td> <td>Unity C#</td>
  </tr>
  
  <tr>
    <td>GitHub</td> <td><a href="https://github.com/kumamoooooon0202/RabbitFrog.git">GitHubはこちら</a></td>
  </tr>
  
  <tr>
    <td>動画</td> <td>準備中</td>
  </tr>
</table>

# 主な作業内容

## キャラクター周り全般
## 編成画面
### 設定した編成を反映する処理
```csharp
[System.Serializable]
public class AutoDeckArray
{
    [Header("自動セットしたいカード")]
    public CardPoolObject[] autoSet = new CardPoolObject[8];
}
```

```csharp
public class AutoDeckSet : MonoBehaviour
{
    private int deckNumber = 0;
    private int countUpDeckNumber
    { 
        get
        {
            deckNumber++;
            if (autoDeckArray.Length <= deckNumber) { deckNumber = 0; }
            return deckNumber;
        }
    }
    [SerializeField] private Image deckImage = null;
    [SerializeField] private Sprite[] deckSetImages = new Sprite[4];
    [SerializeField] private GameObject[] decks = new GameObject[8];
    [Header("おすすめ編成のパターン")] public AutoDeckArray[] autoDeckArray;
    
    public void AutoSetDeck()
    {
        for(int i = 0; i < decks.Length; i++)
        {
            var deck = decks[i].GetComponent<DeckObject>();
            deck.cardPoolObject = autoDeckArray[deckNumber].autoSet[i];
            deck.iconImage.sprite = decks[i].GetComponent<DeckObject>().cardPoolObject.character.image;
            deck.characterIconImage.sprite = decks[i].GetComponent<DeckObject>().cardPoolObject.character.characteristicIcon;
            deck.costText.text = decks[i].GetComponent<DeckObject>().cardPoolObject.character.cost.ToString();
            deck.nowSprite = decks[i].GetComponent<DeckObject>().iconImage.sprite;
        }
        deckImage.sprite = deckSetImages[countUpDeckNumber];
    }
}
```

```csharp

```

```csharp

```

```csharp

```

