---
title: '方法 : カスタム トークンを作成する'
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- SecurityTokenParameters class
- security [WCF], creating custom tokens
- WSSecurityTokenSerializer class
- SecurityToken class
ms.assetid: 6d892973-1558-4115-a9e1-696777776125
ms.openlocfilehash: fd168bf2e5233d9119872b267aea466a7af07041
ms.sourcegitcommit: fb78d8abbdb87144a3872cf154930157090dd933
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47199269"
---
# <a name="how-to-create-a-custom-token"></a>方法 : カスタム トークンを作成する
ここでは、<xref:System.IdentityModel.Tokens.SecurityToken> を使用してカスタムのセキュリティ トークンを作成する方法と、作成したトークンを、カスタム セキュリティ トークン プロバイダーおよび認証システムと統合する方法について説明します。 完全なコード例については、[カスタム トークン](../../../../docs/framework/wcf/samples/custom-token.md)サンプル。  
  
 A*セキュリティ トークン*は基本的に、SOAP メッセージ内の送信者に関するクレームを表す、Windows Communication Foundation (WCF) のセキュリティ フレームワークによって使用される XML 要素。 WCF セキュリティは、システム指定の認証モードのさまざまなトークンを提供します。 たとえば、<xref:System.IdentityModel.Tokens.X509SecurityToken> クラスによって表される X.509 証明書セキュリティ トークンや、<xref:System.IdentityModel.Tokens.UserNameSecurityToken> クラスによって表されるユーザー名セキュリティ トークンなどがあります。  
  
 認証モードや資格情報は、指定した種類のトークンではサポートされないことがあります。 そのような場合は、SOAP メッセージ内部のカスタム資格情報の XML 表現を提供するカスタム セキュリティ トークンを作成する必要があります。  
  
 次の手順では、カスタム セキュリティ トークンを作成する方法と、WCF のセキュリティ インフラストラクチャと統合する方法を示しています。 ここでは、クライアントのクレジット カードに関する情報をサーバーに渡す際に使用するクレジット カード トークンを作成します。  
  
 カスタム資格情報とセキュリティ トークン マネージャーの詳細については、次を参照してください。[チュートリアル: カスタムのクライアントを作成し、サービスの資格情報](../../../../docs/framework/wcf/extending/walkthrough-creating-custom-client-and-service-credentials.md)します。  
  
 セキュリティ トークンを表すさまざまなクラスについては、「<xref:System.IdentityModel.Tokens> 名前空間」を参照してください。  
  
 資格情報、セキュリティ トークン マネージャー、およびプロバイダーおよび認証システム クラスの詳細については、次を参照してください。[セキュリティ アーキテクチャ](https://msdn.microsoft.com/library/16593476-d36a-408d-808c-ae6fd483e28f)します。  
  
## <a name="procedures"></a>手順  
 クライアント アプリケーションには、セキュリティ インフラストラクチャにクレジット カード情報を指定する方法を設ける必要があります。 この情報は、カスタムのクライアント資格情報クラスによりアプリケーションに提供されます。 最初に、カスタム クライアント資格情報のクレジット カード情報を表すクラスを作成します。  
  
#### <a name="to-create-a-class-that-represents-credit-card-information-inside-client-credentials"></a>クライアント資格情報内のクレジット カード情報を表すクラスを作成するには  
  
1.  クレジット カード情報を表す新しいクラスをアプリケーションに定義します。 次の例は、`CreditCardInfo` クラスを示しています。  
  
2.  適切なプロパティをクラスに追加し、カスタム トークンに必要な情報をアプリケーションで設定できるようにします。 次の例では、`CardNumber`、`CardIssuer`、および `ExpirationDate` の 3 つのプロパティをクラスに割り当てます。  
  
     [!code-csharp[c_CustomToken#4](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#4)]
     [!code-vb[c_CustomToken#4](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#4)]  
  
 次に、カスタム セキュリティ トークンを表すクラスを作成する必要があります。 このクラスは、WCF のセキュリティ インフラストラクチャとの間のセキュリティ トークンに関する情報を渡す、セキュリティ トークン プロバイダー、認証システム、およびシリアライザー クラスによって使用されます。  
  
#### <a name="to-create-a-custom-security-token-class"></a>カスタム セキュリティ トークン クラスを作成するには  
  
1.  <xref:System.IdentityModel.Tokens.SecurityToken> クラスから派生する新しいクラスを定義します。 `CreditCardToken` という名前のクラスを作成する例を次に示します。  
  
2.  <xref:System.IdentityModel.Tokens.SecurityToken.Id%2A> プロパティをオーバーライドします。 このプロパティを使用して、SOAP メッセージ内の他の要素からセキュリティ トークンの XML 表現をポイントするためのセキュリティ トークンのローカル識別子を取得します。 次の例では、トークン識別子をコンストラクター パラメーターとしてこのプロパティに渡すことも、セキュリティ トークン インスタンスを作成するたびに新しいランダムな識別子を生成することもできます。  
  
3.  <xref:System.IdentityModel.Tokens.SecurityToken.SecurityKeys%2A> プロパティを実装します。 このプロパティは、セキュリティ トークン インスタンスが表すセキュリティ キーのコレクションを返します。 このようなキーは、署名または暗号化 SOAP メッセージの部分に、WCF によって使用できます。 次の例では、クレジット カード セキュリティ トークンにはセキュリティ キーを含めることができません。そのため、実装は、常に空のコレクションを返します。  
  
4.  <xref:System.IdentityModel.Tokens.SecurityToken.ValidFrom%2A> プロパティと <xref:System.IdentityModel.Tokens.SecurityToken.ValidTo%2A> プロパティをオーバーライドします。 これらのプロパティは、セキュリティ トークン インスタンスの有効性を判断する、WCF によって使用されます。 次の例では、クレジット カード セキュリティ トークンに有効期限のみが含まれているため、`ValidFrom` プロパティは、インスタンスの作成日時を表す <xref:System.DateTime> を返します。  
  
     [!code-csharp[c_CustomToken#1](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#1)]
     [!code-vb[c_CustomToken#1](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#1)]  
  
 セキュリティ トークンの新しい種類を作成したときは、<xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters> クラスの実装が必要です。 この実装をセキュリティ バインド要素構成で使用して、新しいトークンの種類を表します。 セキュリティ トークンのパラメーター クラスは、メッセージを処理するときに実際のセキュリティ トークン インスタンスを一致させるためのテンプレートとして使用できます。 このテンプレートは、セキュリティ トークンを使用したり、認証したりする際に適合する必要がある条件を指定するためにアプリケーションで使用できる追加のプロパティを提供します。 次の例では、WCF インフラストラクチャが使用するかを検証するセキュリティ トークン インスタンスを検索するときにトークンの種類が一致するセキュリティのみ、追加のプロパティは追加されません。  
  
#### <a name="to-create-a-custom-security-token-parameters-class"></a>カスタム セキュリティ トークン パラメーター クラスを作成するには  
  
1.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters> クラスから派生する新しいクラスを定義します。  
  
2.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.CloneCore%2A> メソッドを実装します。 クラスで内部フィールドが定義されている場合は、それらをすべてコピーします。 次の例では、追加のフィールドを定義しません。  
  
3.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.SupportsClientAuthentication%2A> の読み取り専用プロパティを実装します。 このクラスで表されているセキュリティ トークンの種類を使用して、サービスに対するクライアントの認証が可能な場合、このプロパティは `true` を返します。 次の例では、クレジット カード セキュリティ トークンを使用して、サービスに対するクライアントの認証を行うことができます。  
  
4.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.SupportsServerAuthentication%2A> の読み取り専用プロパティを実装します。 このクラスで表されているセキュリティ トークンの種類を使用して、クライアントに対するサービスの認証が可能な場合、このプロパティは `true` を返します。 次の例では、クレジット カード セキュリティ トークンを使用して、クライアントに対するサービスの認証を行うことができません。  
  
5.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.SupportsClientWindowsIdentity%2A> の読み取り専用プロパティを実装します。 このクラスで表されているセキュリティ トークンの種類を Windows アカウントにマップできる場合、このプロパティは `true` を返します。 その場合、認証結果が <xref:System.Security.Principal.WindowsIdentity> クラス インスタンスによって表されます。 次の例では、Windows アカウントにトークンをマップすることができません。  
  
6.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.CreateKeyIdentifierClause%28System.IdentityModel.Tokens.SecurityToken%2CSystem.ServiceModel.Security.Tokens.SecurityTokenReferenceStyle%29> メソッドを実装します。 このセキュリティ トークン パラメーター クラスによって表されるセキュリティ トークン インスタンスへの参照を必要とする場合、このメソッドが WCF のセキュリティ フレームワークによって呼び出されます。 このメソッドには、実際のセキュリティ トークン インスタンスと、要求されている参照の型を指定する <xref:System.ServiceModel.Security.Tokens.SecurityTokenReferenceStyle> の両方が引数として渡されます。 次の例では、内部参照だけがクレジット カード セキュリティ トークンでサポートされます。 <xref:System.IdentityModel.Tokens.SecurityToken> クラスは、内部参照を作成する機能を備えているため、実装には追加のコードが必要ありません。  
  
7.  <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters.InitializeSecurityTokenRequirement%28System.IdentityModel.Selectors.SecurityTokenRequirement%29> メソッドを実装します。 このメソッドは、セキュリティ トークン パラメーター クラス インスタンスのインスタンスに変換する WCF、<xref:System.IdentityModel.Selectors.SecurityTokenRequirement>クラス。 変換結果は、適切なセキュリティ トークン インスタンスを作成するためにセキュリティ トークン プロバイダーによって使用されます。  
  
     [!code-csharp[c_CustomToken#2](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#2)]
     [!code-vb[c_CustomToken#2](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#2)]  
  
 セキュリティ トークンは、SOAP メッセージ内で送信されますが、そのためには、セキュリティ トークンのインメモリ表現とネットワーク上表現の間での変換機構が必要です。 WCF では、セキュリティ トークン シリアライザーを使用して、このタスクを実行します。 すべてのカスタム トークンには、カスタム セキュリティ トークンを SOAP メッセージからシリアル化および逆シリアル化できるカスタム セキュリティ トークン シリアライザーを関連付ける必要があります。  
  
> [!NOTE]
>  派生キーは、既定で有効になっています。 カスタム セキュリティ トークンを作成して、プライマリ トークンとして使用する場合、WCF はそこからキーを派生します。 キーを派生する一方、カスタム セキュリティ トークン シリアライザーが呼び出され、<xref:System.IdentityModel.Tokens.SecurityKeyIdentifierClause> をシリアル化しながら、そのカスタム セキュリティ トークンの `DerivedKeyToken` をネットワークに送出します。 受信側では、ネットワーク外でのトークンの逆シリアル化時に、`DerivedKeyToken` シリアライザーは、トークンのすぐ下のトップレベルの子として `SecurityTokenReference` 要素を必要とします。 カスタム セキュリティ トークン シリアライザーによる句型のシリアル化時に `SecurityTokenReference` 要素が追加されていなかった場合、例外がスローされます。  
  
#### <a name="to-create-a-custom-security-token-serializer"></a>カスタム セキュリティ トークン シリアライザーを作成するには  
  
1.  <xref:System.ServiceModel.Security.WSSecurityTokenSerializer> クラスから派生する新しいクラスを定義します。  
  
2.  <xref:System.ServiceModel.Security.WSSecurityTokenSerializer.CanReadTokenCore%28System.Xml.XmlReader%29> に基づいて XML ストリームを読み取る <xref:System.Xml.XmlReader> メソッドをオーバーライドします。 シリアライザー実装が現在の要素に基づいてセキュリティ トークンを逆シリアル化できる場合、このメソッドは `true` を返します。 次の例では、このメソッドが、XML リーダーの現在の XML 要素が正しい要素名と名前空間を持っているかどうかをチェックします。 持っていない場合は、このメソッドの基本クラスの実装を呼び出して、XML 要素を処理します。  
  
3.  <xref:System.ServiceModel.Security.WSSecurityTokenSerializer.ReadTokenCore%28System.Xml.XmlReader%2CSystem.IdentityModel.Selectors.SecurityTokenResolver%29> メソッドをオーバーライドします。 このメソッドは、セキュリティ トークンの XML コンテンツを読み取り、その適切なメモリ内表現を構築します。 渡された XML リーダーの基盤となる XML 要素を認識できない場合は、基本クラスの実装を呼び出して、システム指定のトークンの種類を処理します。  
  
4.  <xref:System.ServiceModel.Security.WSSecurityTokenSerializer.CanWriteTokenCore%28System.IdentityModel.Tokens.SecurityToken%29> メソッドをオーバーライドします。 引数として渡されるトークンのメモリ内表現を XML 表現に変換できる場合、このメソッドは `true` を返します。 変換できない場合は、基本クラスの実装を呼び出します。  
  
5.  <xref:System.ServiceModel.Security.WSSecurityTokenSerializer.WriteTokenCore%28System.Xml.XmlWriter%2CSystem.IdentityModel.Tokens.SecurityToken%29> メソッドをオーバーライドします。 このメソッドは、セキュリティ トークンのメモリ内表現を XML 表現に変換します。 変換できない場合は、基本クラスの実装を呼び出します。  
  
     [!code-csharp[c_CustomToken#3](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#3)]
     [!code-vb[c_CustomToken#3](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#3)]  
  
 上記の 4 つの手順を完了したら、カスタム セキュリティ トークンをセキュリティ トークン プロバイダー、セキュリティ トークン認証システム、セキュリティ トークン マネージャー、クライアント資格情報、およびサービス資格情報と統合します。  
  
#### <a name="to-integrate-the-custom-security-token-with-a-security-token-provider"></a>カスタム セキュリティ トークンをセキュリティ トークン プロバイダーと統合するには  
  
1.  セキュリティ トークン プロバイダーは、必要に応じて、トークンのインスタンスを作成、変更、および返却します。 カスタム セキュリティ トークンのカスタム プロバイダーを作成するには、<xref:System.IdentityModel.Selectors.SecurityTokenProvider> クラスを継承するクラスを作成します。 <xref:System.IdentityModel.Selectors.SecurityTokenProvider.GetTokenCore%2A> メソッドをオーバーライドして、`CreditCardToken` のインスタンスを返す例を次に示します。 カスタム セキュリティ トークン プロバイダーの詳細については、次を参照してください。[方法: カスタム セキュリティ トークン プロバイダーを作成](../../../../docs/framework/wcf/extending/how-to-create-a-custom-security-token-provider.md)です。  
  
     [!code-csharp[c_CustomToken#6](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#6)]
     [!code-vb[c_CustomToken#6](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#6)]  
  
#### <a name="to-integrate-the-custom-security-token-with-a-security-token-authenticator"></a>カスタム セキュリティ トークンをセキュリティ トークン認証システムと統合するには  
  
1.  セキュリティ トークン認証システムは、セキュリティ トークンがメッセージから抽出されたときにトークンの内容を検証します。 カスタム セキュリティ トークンのカスタム認証システムを作成するには、<xref:System.IdentityModel.Selectors.SecurityTokenAuthenticator> クラスを継承するクラスを作成します。 <xref:System.IdentityModel.Selectors.SecurityTokenAuthenticator.ValidateTokenCore%2A> メソッドをオーバーライドする例を次に示します。 カスタム セキュリティ トークン認証システムの詳細については、次を参照してください。[方法: カスタム セキュリティ トークン オーセンティケーターの作成](../../../../docs/framework/wcf/extending/how-to-create-a-custom-security-token-authenticator.md)です。  
  
     [!code-csharp[c_CustomToken#7](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#7)]
     [!code-vb[c_CustomToken#7](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#7)]  
  
     [!code-csharp[c_CustomToken#12](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#12)]
     [!code-vb[c_CustomToken#12](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#12)]  
  
#### <a name="to-integrate-the-custom-security-token-with-a-security-token-manager"></a>カスタム セキュリティ トークンをセキュリティ トークン マネージャーと統合するには  
  
1.  セキュリティ トークン マネージャーは、トークン プロバイダー、セキュリティ認証システム、およびトークン シリアライザーの適切なインスタンスを作成します。 カスタム トークン マネージャーを作成するには、<xref:System.ServiceModel.ClientCredentialsSecurityTokenManager> クラスを継承するクラスを作成します。 このクラスの主要なメソッドは <xref:System.IdentityModel.Selectors.SecurityTokenRequirement> を使用して、適切なプロバイダーとクライアント資格情報またはサービス資格情報を作成します。 カスタム セキュリティ トークン マネージャーの詳細については、次を参照してください。[チュートリアル: カスタムのクライアントを作成し、サービスの資格情報](../../../../docs/framework/wcf/extending/walkthrough-creating-custom-client-and-service-credentials.md)します。  
  
     [!code-csharp[c_CustomToken#8](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#8)]
     [!code-vb[c_CustomToken#8](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#8)]  
  
     [!code-csharp[c_CustomToken#9](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#9)]
     [!code-vb[c_CustomToken#9](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#9)]  
  
#### <a name="to-integrate-the-custom-security-token-with-custom-client-and-service-credentials"></a>カスタム セキュリティ トークンをカスタムのクライアント資格情報およびサービス資格情報と統合するには  
  
1.  アプリケーションに API を提供し、事前に作成したカスタム セキュリティ トークン インフラストラクチャで、カスタム セキュリティ トークンの内容を提供および認証する際に使用するカスタム トークン情報を指定できるようにするには、カスタムのクライアント資格情報とサービス資格情報を追加する必要があります。 この方法を次の例に示します。 カスタムのクライアントとサービスの資格情報の詳細については、次を参照してください。[チュートリアル: カスタムのクライアントを作成し、サービスの資格情報](../../../../docs/framework/wcf/extending/walkthrough-creating-custom-client-and-service-credentials.md)します。  
  
     [!code-csharp[c_CustomToken#10](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#10)]
     [!code-vb[c_CustomToken#10](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#10)]  
  
     [!code-csharp[c_customToken#11](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#11)]
     [!code-vb[c_customToken#11](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#11)]  
  
 以前作成したカスタム セキュリティ トークン パラメーター クラスは、サービスと通信するときに、カスタム セキュリティ トークンを使用する必要があります、WCF のセキュリティ フレームワークに通知に使用されます。 この方法を次の手順に示します。  
  
#### <a name="to-integrate-the-custom-security-token-with-the-binding"></a>カスタム セキュリティ トークンをバインディングと統合するには  
  
1.  <xref:System.ServiceModel.Channels.SecurityBindingElement> クラスで公開されるトークン パラメーター コレクションの 1 つで、カスタム セキュリティ トークン パラメーター クラスを指定する必要があります。 次の例では、`SignedEncrypted` によって返されるコレクションを使用します。 このコードでは、クライアントからサービスに送信されるすべてのメッセージにクレジット カードのカスタム トークンを追加し、メッセージの内容に自動的に署名して暗号化します。  
  
     [!code-csharp[c_CustomToken#13](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_customtoken/cs/source.cs#13)]
     [!code-vb[c_CustomToken#13](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_customtoken/vb/source.vb#13)]  
  
 このトピックでは、カスタム トークンを実装および使用するために必要なさまざまなコードを示します。 方法の完全な例を参照してください。 これらすべてのコードの情報をまとめる」を参照して[カスタム トークン](../../../../docs/framework/wcf/samples/custom-token.md)します。  
  
## <a name="see-also"></a>関連項目  
 <xref:System.IdentityModel.Tokens.SecurityToken>  
 <xref:System.ServiceModel.Security.Tokens.SecurityTokenParameters>  
 <xref:System.ServiceModel.Security.WSSecurityTokenSerializer>  
 <xref:System.IdentityModel.Selectors.SecurityTokenProvider>  
 <xref:System.IdentityModel.Selectors.SecurityTokenAuthenticator>  
 <xref:System.IdentityModel.Policy.IAuthorizationPolicy>  
 <xref:System.IdentityModel.Selectors.SecurityTokenRequirement>  
 <xref:System.IdentityModel.Selectors.SecurityTokenManager>  
 <xref:System.ServiceModel.Description.ClientCredentials>  
 <xref:System.ServiceModel.Description.ServiceCredentials>  
 <xref:System.ServiceModel.Channels.SecurityBindingElement>  
 [チュートリアル: カスタム クライアントおよびサービスの資格情報を作成する](../../../../docs/framework/wcf/extending/walkthrough-creating-custom-client-and-service-credentials.md)  
 [方法 : カスタム セキュリティ トークン認証システムを作成する](../../../../docs/framework/wcf/extending/how-to-create-a-custom-security-token-authenticator.md)  
 [方法 : カスタム セキュリティ トークン プロバイダーを作成する](../../../../docs/framework/wcf/extending/how-to-create-a-custom-security-token-provider.md)  
 [セキュリティ アーキテクチャ](https://msdn.microsoft.com/library/16593476-d36a-408d-808c-ae6fd483e28f)
