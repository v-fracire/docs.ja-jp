### <a name="no-longer-able-to-set-enableviewstatemac-to-false"></a>EnableViewStateMac を false に設定できなくなった

|   |   |
|---|---|
|説明|ASP.NET では、開発者は <code>&lt;pages enableViewStateMac=&quot;false&quot;/&gt;</code> または <code>&lt;@Page EnableViewStateMac=&quot;false&quot; %&gt;</code> を指定できなくなりました。 ビュー ステートのメッセージ認証コード (MAC) は、ビュー ステートが埋め込まれたすべての要求に強制されています。 EnableViewStateMac プロパティを明示的に <code>false</code> に設定するアプリのみが影響を受けます。|
|提案される解決策|EnableViewStateMac は true であると想定する必要があり、結果としての MAC エラーを解決する必要があります ([このガイダンス](https://support.microsoft.com/kb/2915218)で説明されているように、MAC エラーの原因の詳細によって複数の解決策があります)。|
|スコープ|Major|
|Version|4.5.2|
|型|ランタイム|

