---
title: "Azure Machine Learning データ準備で新しい列を派生させるためのサンプル Python | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備で新しい列を作成するための Python コード例について説明します。"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e576d44a854159054d4f7886fe7859ae34875c8f
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>カスタム列変換のサンプル (Python) 
メニュー内でのこの変換の名前は、**列の追加 (スクリプト)** です。

この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事をご覧ください。

## <a name="test-equivalence-and-replace-values"></a>等価性をテストし、値を置換します 
Col1 の値が 4 より下の場合、新しい列には 1 の値が入ります。 Col1 の値が 4 より上の場合、新しい列には 2 の値が入ります。 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>現在の日付と時刻 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>型キャスト 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>null 値の有無の評価 
Col1 に null 値が含まれている場合、新しい列に **Bad** というマークが付きます。 含まれていない場合は、**Good** というマークが付きます。 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>新しい計算列 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>エポックの計算 
Unix エポック以降の秒数 (Col1 がすでに日付であると想定): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>列値を新しい列にハッシュする
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




