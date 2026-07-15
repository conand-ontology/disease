# CONAND (Diseases)
Disease part of CONAND (Clinical Ontology in Anatomical Structure and Disease)

[臨床医学オントロジーCONAND](http://onto.bmi.m.u-tokyo.ac.jp/)の疾患オントロジーの公開用レポジトリです．  
現時点では仮公開のため，今後，変更の可能性があります．

## 公開中のファイル
- [CONAND-disease.ttl](CONAND-disease.ttl)  疾患定義となる因果連鎖（疾患定義連鎖，派生連鎖）  
- [CONAND-abnormalState.ttl](CONAND-abnormalState.ttl)  異常状態の汎用連鎖  

## SPARQLエンドポイント(仮運用)
- [Webブラウザからの検索](https://yasgui.triply.cc/?endpoint=https://lod.hozo.jp/kz-fuseki/CONANDpre/sparql)
- プログラムからのアクセスには `https://lod.hozo.jp/kz-fuseki/CONANDpre/sparql` を利用

## SPARQLクエリ例
### 例１：「糖尿病」の疾患定義（疾患連鎖）を取得
```SPARQL
prefix conand: <http://conand.bmi.m.u-tokyo.ac.jp/disease/>
prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?dis ?abn ?abnLabel ?result ?resultLabel
WHERE {
  ?dis a conand:Disease;
  rdfs:label "糖尿病"@ja;
  conand:hasCoreState|conand:hasDerivedState ?abn.
  ?abn rdfs:label ?abnLabel.
  ?abn conand:hasResult ?result.
  ?result rdfs:label ?resultLabel .
}
```

### 例２：「高血糖」の結果の汎用連鎖を取得
```SPARQL
prefix conand: <http://conand.bmi.m.u-tokyo.ac.jp/disease/>
prefix rdfs:  <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?causeLabel  ?resultLabel
WHERE {
#異常状態名を指定して，結果を探索
  ?abn rdfs:subClassOf conand:AbnormalState;
      rdfs:label "高血糖"@ja .
  ?abn conand:hasResult/conand:hasResult* ?result.
#得られた異常状態の原因-結果のペアを取得
  ?cause conand:hasResult ?result.
  ?cause rdfs:label ?causeLabel .
  ?result rdfs:label ?resultLabel .
}
```

### 例３：疾患定義・派生連鎖と汎用連鎖の組み合わせ
```SPARQL
prefix conand: <http://conand.bmi.m.u-tokyo.ac.jp/disease/>
prefix rdfs:  <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?disLabel ?result ?resultLabel
WHERE {
  ?abn rdfs:subClassOf conand:AbnormalState;
      rdfs:label "高血糖"@ja .
  ?abn conand:hasResult ?result.
  ?result rdfs:label ?resultLabel .
  
  ?abnResult a ?result.
  ?dis conand:hasCoreState|conand:hasDerivedState ?abnResult;
       rdfs:label ?disLabel.
}
```


