---
title: 部分的なエラーを処理するための戦略
description: コンテナー化された .NET アプリケーションの .NET マイクロサービス アーキテクチャ | 部分的なエラーを処理するための戦略
author: CESARDELATORRE
ms.author: wiwagn
ms.date: 06/08/2018
ms.openlocfilehash: ba15258be8caa1a5ed800cef0ebe832aa7328252
ms.sourcegitcommit: ccd8c36b0d74d99291d41aceb14cf98d74dc9d2b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53146119"
---
# <a name="strategies-for-handling-partial-failure"></a>部分的なエラーを処理するための戦略

部分的なエラーを処理するための戦略には、次のものがあります。

**内部マイクロサービス全体で非同期通信 (メッセージ ベースの通信など) を使用する**。 複数の内部マイクロサービスに渡って同期 HTTP 呼び出しの長いチェーンを作成しないことを強くお勧めします。こうした不適切な設計を行うと、最終的には異常停止の主要な原因になるからです。 反対に、最初の要求/応答サイクルの後は、内部マイクロサービス全体で非同期 (メッセージベース) 通信のみを使用することをお勧めします (ただし、第 1 レベルのマイクロサービスや細かい設定が可能な API ゲートウェイと、クライアント アプリケーション間でのフロントエンド通信は除きます)。 その結果として、一貫性およびイベント ドリブンのアーキテクチャが実現され、連鎖反応を最小限に抑えることができます。 この方法により、マイクロサービスの自律性が高いレベルで達成され、上記の問題を防止することができます。

**指数のバックオフを含む再試行を使用する**。 この手法は、サービスが短時間だけ使用不可になったときに一定回数、呼び出しを再試行するもので、短期的なエラーや断続的なエラーを回避するのに役立ちます。 この状況は、ネットワークの問題が断続的に発生したり、マイクロサービス/コンテナーがクラスター内の別のノードに移動したときに発生する場合があります。 ただし、これらの再試行がサーキット ブレーカーを使用して適切に設計されていない場合は、連鎖反応が拡大し、最終的には[サービス拒否 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) が発生することもあります。

**ネットワークのタイムアウトを回避する**。 一般に、クライアントは、無期限にブロックしないように設計する必要があります。また、応答を待機するときは、必ずタイムアウトを使用するように設計する必要があります。 タイムアウトを使用すると、リソースがいつまで拘束されることがなくなります。

**サーキット ブレーカー パターンを使用する**。 この方法では、クライアント プロセスは、失敗した要求の数を追跡します。 エラー率が設定値を超えると、"サーキット ブレーカー" が作動し、以降の試行が直ちに失敗します。 (多数の要求が失敗している場合は、サービスが使用不可であり、要求を送信しても意味がありません。)タイムアウト期間が過ぎたら、クライアントは、再試行する必要があります。新しい要求が成功した場合は、サーキット ブレーカーを閉じます。

**フォールバックを実行する**。 この方法の場合、クライアント プロセスは、要求が失敗したときに、キャッシュ データや既定値を返すなどのフォールバック ロジックを実行します。 これは、クエリに適した方法で、更新やコマンドの場合は、複雑さが増します。

**キューに格納する要求の数を制限する**。 クライアントは、クライアント マイクロサービスが特定のサービスに送信できる未処理の要求数に対して、上限を課す必要もあります。 制限に達した場合は、追加の要求を行ってもたいていは無意味で、それらの試みは直ちに失敗します。 実装に関しては、Polly の [バルクヘッド分離](https://github.com/App-vNext/Polly/wiki/Bulkhead)ポリシーを使用して、この要件を満たすことができます。 この方法は、本質的には、<xref:System.Threading.SemaphoreSlim> を実装として使用した並列化スロットルです。 バルクヘッドの外側の "キュー" も許可されます。 (たとえば、容量が一杯であると思われた理由で) 実行前であっても、過度の負荷をプロアクティブに取り除くことができます。 これにより、特定のエラー シナリオへの対処をサーキット ブレーカーよりも速く行うことができます (サーキット ブレーカーは、エラーを待機しているため)。 Polly の BulkheadPolicy オブジェクトを見ると、バルクヘッドとキューがどのくらい一杯になっているかがわかります。また、このオブジェクトは、オーバーフローに関するイベントを提供するので、自動水平スケールの駆動に使用することもできます。

## <a name="additional-resources"></a>その他の技術情報

-   **回復性パターン**
    [*https://docs.microsoft.com/azure/architecture/patterns/category/resiliency*](https://docs.microsoft.com/azure/architecture/patterns/category/resiliency)

-   **回復性の追加とパフォーマンスの最適化**
    [*https://msdn.microsoft.com/library/jj591574.aspx*](https://msdn.microsoft.com/library/jj591574.aspx)

-   **Bulkhead** (バルクヘッド)。 GitHub リポジトリ。 Polly ポリシーを使用した実装。\
    [*https://github.com/App-vNext/Polly/wiki/Bulkhead*](https://github.com/App-vNext/Polly/wiki/Bulkhead)

-   **Azure 用の回復性の高いアプリケーションの設計**
    [*https://docs.microsoft.com/azure/architecture/resiliency/*](https://docs.microsoft.com/azure/architecture/resiliency/)

-   **一時的フォールト処理**
    <https://docs.microsoft.com/azure/architecture/best-practices/transient-faults>

>[!div class="step-by-step"]
>[前へ](handle-partial-failure.md)
>[次へ](implement-retries-exponential-backoff.md)