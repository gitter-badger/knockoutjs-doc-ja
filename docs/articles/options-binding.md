# "option" バインディング

<!--
鈴木翻訳中
-->

### 用途 {#purpose}

`options` バインディングは `<select>` によるドロップダウンリスト、または `<select size='6'>` のような複数選択リストの選択肢を制御します。
このバインディングは `<select>` エレメントのみで使用できます。

紐付ける値は配列または ObservableArray です。`<select>` エレメントは配列の各アイテムから一つを表示します。

※ 複数選択リストの場合、選択状態をセットまたは取得するのに [`selectedOptions` バインディング](selectedOptions-binding) を使います。
択一のドロップダウンリストでは、他のフォーム部品同様に選択されたアイテムを [`value` バインディング](value-binding) で管理できます。

### 例1: ドロップダウンリスト {#example-1-drop-down-list}

```html
<p>
    行き先:
    <select data-bind="options: availableCountries"></select>
</p>
 
<script type="text/javascript">
    var viewModel = {
        // 選択肢の初期値を設定
        availableCountries: ko.observableArray(['フランス', 'ドイツ', 'スペイン'])
    };
 
    // ... その後 ...
    viewModel.availableCountries.push('中国'); // 選択肢を追加
</script>
```

### 例2: 複数選択リスト {#example-2-multi-select-list}

```html
<p>
    言ってみたい国はどこですか:
    <select data-bind="options: availableCountries" size="5" multiple="true"></select>
</p>
 
<script type="text/javascript">
    var viewModel = {
        availableCountries: ko.observableArray(['フランス', 'ドイツ', 'スペイン'])
    };
</script>
```


### 例3: 単なる文字列ではなく任意の JavaScript オブジェクトを表示 {#example-3-drop-down-list-representing-arbitrary-javascript-objects-not-just-strings}
```html
<p>
    あなたの国:
    <select data-bind="options: availableCountries,
                       optionsText: 'countryName',
                       value: selectedCountry,
                       optionsCaption: '-選択してください-'"></select>
</p>
 
<div data-bind="visible: selectedCountry"> <!-- どれかを選択したときに表示される -->
    選択した国およびその人口
    <span data-bind="text: selectedCountry() ? selectedCountry().countryPopulation : '不明'"></span>.
</div>
 
<script type="text/javascript">
    // 2つのプロパティを持ったオブジェクトのコンストラクタ
    var Country = function(name, population) {
        this.countryName = name;
        this.countryPopulation = population;
    };
 
    var viewModel = {
        availableCountries : ko.observableArray([
            new Country("イギリス", 65000000),
            new Country("アメリカ", 320000000),
            new Country("スウェーデン", 29000000)
        ]),
        selectedCountry : ko.observable() // デフォルトでは何も選択されていない状態
    };
</script>
```

### 例4: 表示される選択肢のテキストとして、任意の JavaScript オブジェクトを処理した値を用いる  {#example-4-drop-down-list-representing-arbitrary-javascript-objects-with-displayed-text-computed-as-a-function-of-the-represented-item}

```html
<!-- 下記の select 以外は例3と同じ: -->
<select data-bind="options: availableCountries,
                   optionsText: function(item) {
                       return item.countryName + ' (pop: ' + item.countryPopulation + ')'
                   },
                   value: selectedCountry,
                   optionsCaption: '-選択してください-'"></select>
```

例3と例4の違いは `optionsText` に指定した値のみです。

# パラメタ {#parameters}

- 主パラメタ
	
	配列または ObservableArray を指定します。Knockout は紐付けられた `<select>` ノードに各アイテムごとに `<option>` エレメントを追加します。既存の選択肢は削除されます。
	
	文字列の配列を指定した場合、他に必要なパラメタはありません。`<select>` エレメントにそれぞれの文字列が選択肢として表示されます。ただし、単なる文字列ではなく任意の JavaScript オブジェクトをユーザーに選択してほしい場合は、`optionsText` と `optionsValue` パラメタを確認して下さい。
	
	このパラメタが Observable である場合、このバインディングは値が変更される度に選択肢をを更新します。Observable でない場合は、選択肢は一度だけ設定され、以降は更新されません。
    
- 追加パラメタ

	- `optionsCaption`
		
		時にはデフォルトでどの選択肢も選択されていない状態にしたいことがあります。しかし択一のドロップダウンリストでは通常、初期状態でいずれかが選択されてしまいます。どうしたら何も選択されないようにできるでしょう？よくある解決策として、選択肢リストに「選択してください」のようなダミーの選択肢を用意し、デフォルトでそれが選択されるようにします。
		
		これは `optionsCaption` という追加パラメタで次のように未選択状態のテキストを設定することで簡単に実現できます。
        
        `<select data-bind='options: myOptions, optionsCaption: "選択してください", value: myChosenValue'></select>`
        
        Knockout はリストの先頭に「選択してください」というテキストアイテムを、value を `undefined` として追加します。したがって `myChosenValue` の値が `undefined` であれば、そのダミーの選択肢が選択されます。`optionsCaption` に Observable を指定した場合、値の変更に従い初期アイテムのテキストも更新されます。
        
	- `optionsText`
		
		上記の例3では `options` で単なる文字列ではなく任意の JavaScript オブジェクトの配列をバインドする方法を示しました。この場合、オブジェクトのどのおプロパティを選択肢のテキストとして表示すべきかを選ぶ必要があります。例3で `optionsText` パラメタを使って指定しました。
		
		各アイテムのプロパティの値をそのまま表示したくない場合、`optionsText` に表示されるテキストを処理するための任意の JavaScript 関数を設定することができます。上記の例4にて、複数のプロパティの値を結合して表示する方法を示しました。
        
	- `optionsValue`
		
		`optionsText` と同様に、`optionsValue` パラメタによって Knockout が `<option>` エレメントを生成する際にオブジェクトのどのプロパティを `value` として設定すべきかを指定することができます。またこの値を決定するための関数を指定することもできます。この関数は選択されたアイテムを引数として受け取り、`<option>` エレメントの value 属性として使う文字列を返すようにします。
		
		一般的に `optionsValue` は、利用可能な選択肢を更新する際に Knockout が正しく選択を保持することを保証するための方法として使います。例えば選択肢として “car” オブジェクトのリストが Ajax によって何度も取得され、かつ選択した自動車が記憶されるようにしたいとします。`optionsValue` に `"carId"` など、“car” オブジェクトを一意に特定するプロパティを指定しないと、リストが更新されたときに、前に選択されていたものに相当するオブジェクトがどれか Knockout が知ることができなくなります。
	
	- `optionsIncludeDestroyed`
		
		Sometimes you may want to mark an array entry as deleted, but without actually losing record of its existence. This is known as a non-destructive delete. For details of how to do this, see [the destroy function on `observableArray`](observableArrays#destroy-and-destroyall).
        
        By default, the options binding will skip over (i.e., hide) any array entries that are marked as destroyed. If you want to show destroyed entries, then specify this additional parameter like:
        
        `<select data-bind='options: myOptions, optionsIncludeDestroyed: true'></select>`
        
	- `optionsAfterRender`
		
		If you need to run some further custom logic on the generated `option` elements, you can use the `optionsAfterRender` callback. See Note 2 below.
	
	- `selectedOptions`
	
		For a multi-select list, you can read and write the selection state using `selectedOptions`. Technically this is a separate binding, so it has [its own documentation](electedOptions-binding).
		
	- `valueAllowUnset`
		
		If you want Knockout to allow your model property to take values that have no corresponding entry in your `<select>` element (and display this by making the `<select>` element blank), then see [documentation for `valueAllowUnset`](value-binding#using-valueallowunset-with-select-elements).
	
### Note 1: Selection is preserved when setting/changing options {#note-1-selection-is-preserved-when-settingchanging-options}

When the `options` binding changes the set of options in your `<select>` element, KO will leave the user’s selection unchanged where possible. So, for a single-select drop-down list, the previously selected option value will still be selected, and for a multi-select list, all the previously selected option values will still be selected (unless, of course, you’ve removed one or more of those options).

That’s because the `options` binding tries to be independent of the `value` binding (which controls selection for a single-select list) and the `selectedOptions` binding (which controls selection for a multi-select list).

### Note 2: Post-processing the generated options {#note-2-post-processing-the-generated-options}

If you need to run some further custom logic on the generated `option` elements, you can use the `optionsAfterRender` callback. The callback function is invoked each time an `option` element is inserted into the list, with the following parameters:

1. The inserted `option` element
1. The data item against which it is bound, or `undefined` for the caption element

Here’s an example that uses optionsAfterRender to add a disable binding to each option.

```html
<select size=3 data-bind="
    options: myItems,
    optionsText: 'name',
    optionsValue: 'id',
    optionsAfterRender: setOptionDisable">
</select>
 
<script type="text/javascript">
    var vm = {
        myItems: [
            { name: 'Item 1', id: 1, disable: ko.observable(false)},
            { name: 'Item 3', id: 3, disable: ko.observable(true)},
            { name: 'Item 4', id: 4, disable: ko.observable(false)}
        ],
        setOptionDisable: function(option, item) {
            ko.applyBindingsToNode(option, {disable: item.disable}, item);
        }
    };
    ko.applyBindings(vm);
</script>
```

### Dependencies {#dependencies}

None, other than the core Knockout library.