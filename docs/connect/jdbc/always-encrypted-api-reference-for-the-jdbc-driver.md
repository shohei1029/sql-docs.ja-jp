---
description: JDBC ドライバーの Always Encrypted API のリファレンス
title: JDBC ドライバーの Always Encrypted API について、また、それらを使用して Java アプリケーションのデータを暗号化およびセキュリティ保護する方法について説明します。
ms.custom: ''
ms.date: 01/29/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 6962a2aa-9508-4d4f-a78c-905e2bc68615
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: d44b4b4858b71af4e633f7057ca77fd37ea0ce75
ms.sourcegitcommit: 524a0f0cc9533188f4b14d2e78ba1cfe816b3b9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2021
ms.locfileid: "105633222"
---
# <a name="always-encrypted-api-reference-for-the-jdbc-driver"></a>JDBC ドライバーの Always Encrypted API のリファレンス

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

Always Encrypted を使用すると、クライアントはサーバーに暗号化キーを開示することなく、クライアント アプリケーション内の機微なデータを暗号化することができます。 クライアント コンピューターにインストールされているドライバーの Always Encrypted を有効にすると、クライアント アプリケーション内の機密データを自動的に暗号化および暗号化解除することで、この機能が実行されます。

ドライバーにより、SQL Server にデータを渡す前に機密性の高い列のデータが暗号化され、アプリケーションに対するセマンティクスを維持するために自動的にクエリが書き換えられます。 同様に、ドライバーはクエリ結果内の暗号化されたデータベース列に格納されているデータを透過的に暗号化解除します。 詳細については、[Always Encrypted (データベース エンジン)](../../relational-databases/security/encryption/always-encrypted-database-engine.md)に関するページと「[JDBC ドライバーでの Always Encrypted の使用](using-always-encrypted-with-the-jdbc-driver.md)」を参照してください。

> [!NOTE]
> Always Encrypted は、Azure SQL Database および SQL Server 2016 以降で使用される Microsoft JDBC Driver 6.0 for SQL Server 以降でのみサポートされています。

## <a name="always-encrypted-api-references"></a>Always Encrypted API リファレンス

Always Encrypted を使用するクライアント アプリケーションで使用するために、JDBC ドライバー API に対していくつかの新たな追加と変更が施されています。

### <a name="sqlserverconnection-class"></a>SQLServerConnection クラス

|名前|説明|
|----------|-----------------|
|新しい接続文字列キーワード:<br /><br /> columnEncryptionSetting|columnEncryptionSetting=Enabled は接続で Always Encrypted 機能を有効にし、columnEncryptionSetting=Disabled はこの機能を無効にします。 指定できる値は、Enabled または Disabled です。 既定値は Disabled です。|
|新しい接続文字列キーワード: (MS JDBC 7.4 以降)<br /><br /> keyVaultProviderClientId <br /><br /> keyVaultProviderClientKey |keyVaultProviderClientId=\<ClientID>;keyVaultProviderClientKey=\<ClientKey> <br/><br/> SQLServerColumnEncryptionAzureKeyVaultProvider を登録し、クライアント ID とクライアント キーの値を使用して Azure Key Vault から列マスター キーを取得します|
|新しいメソッド:<br /><br />`public static void setColumnEncryptionTrustedMasterKeyPaths(Map<String, List\<String>> trustedKeyPaths)`<br /><br />`public static void updateColumnEncryptionTrustedMasterKeyPaths(String server, List\<String> trustedKeyPaths)`<br /><br />`public static void removeColumnEncryptionTrustedMasterKeyPaths(String server)`|データベース サーバーの信頼されたキー パスの一覧を設定、更新、削除できます。 アプリケーション クエリの処理中に、一覧にないキー パスをドライバーが受け取った場合、クエリは失敗します。 このプロパティを使用すると、セキュリティが侵害され、偽のキー パスを送信し、キー ストアの資格情報漏洩につながるおそれがあるサーバーを含めたセキュリティ攻撃に対して、セキュリティ保護がさらに強化されます。|
|新しいメソッド:<br /><br />`public static Map<String, List\<String>> getColumnEncryptionTrustedMasterKeyPaths()`|データベース サーバーの信頼されたキー パスの一覧を返します。|
|新しいメソッド:<br /><br />`public static void registerColumnEncryptionKeyStoreProviders (Map\<String, SQLServerColumnEncryptionKeyStoreProvider> clientKeyStoreProviders)`|カスタム キー ストア プロバイダーを登録できます。 これは、キー ストア プロバイダー名をキー ストア プロバイダー実装にマップするディクショナリです。<br /><br /> JVM キーストアを使用するには、JVM キーストアの資格情報を使用して SQLServerColumnEncryptionJVMKeyStoreProvider オブジェクトをインスタンス化し、ドライバーに登録する必要があります。 このプロバイダーの名前は 'MSSQL_JVM_KEYSTORE' である必要があります。<br /><br /> Azure Key Vault キーストアを使用するには SQLServerColumnEncryptionAzureKeyStoreProvider オブジェクトをインスタンス化し、ドライバーに登録する必要があります。 このプロバイダーの名前は "AZURE_KEY_VAULT" である必要があります。|
|新しいメソッド:<br /><br />`public static void unregisterColumnEncryptionKeyStoreProviders (Map\<String, SQLServerColumnEncryptionKeyStoreProvider> clientKeyStoreProviders)`|キー ストア プロバイダーの名前をキー ストア プロバイダーの実装にマップする辞書をクリアすることで、すべてのカスタム キー ストア プロバイダーの登録を解除できます。|
|`public final boolean getSendTimeAsDatetime()`|sendTimeAsDatetime 接続プロパティの設定が返されます。|
|`public void setSendTimeAsDatetime(boolean sendTimeAsDateTimeValue)`|sendTimeAsDatetime 接続プロパティの設定を変更します。|

### <a name="sqlserverconnectionpoolproxy-class"></a>SQLServerConnectionPoolProxy クラス

|名前|説明|
|----------|-----------------|
|`public final boolean getSendTimeAsDatetime()` | sendTimeAsDatetime 接続プロパティの設定が返されます。|
|`public void setSendTimeAsDatetime(boolean sendTimeAsDateTimeValue)` | sendTimeAsDatetime 接続プロパティの設定を変更します。|

### <a name="sqlserverdatasource-class"></a>SQLServerDataSource クラス

|名前|説明|
|----------|-----------------|
|`public void setColumnEncryptionSetting(String columnEncryptionSetting)`|データ ソース オブジェクトに対して Always Encrypted 機能を有効または無効にします。<br /><br /> 既定値は Disabled です。|
|`public String getColumnEncryptionSetting()`|データ ソース オブジェクトに対して Always Encrypted 機能を取得します。|
|`public void setKeyStoreAuthentication(String keyStoreAuthentication)`|キー ストアを識別する名前を設定します。 サポートされている値は、Java キー ストアを示す **JavaKeyStorePassword** だけです。<br/><br/>既定値は null です。|
|`public String getKeyStoreAuthentication()`|データ ソース オブジェクトに対する keyStoreAuthentication の設定の値を取得します。|
|`public void setKeyStoreSecret(String keyStoreSecret)`|Java キーストアのパスワードを設定します。 キーストアとキーのパスワードは同じである必要があります。 keyStoreAuthentication は **JavaKeyStorePassword** を使用して設定する必要があります。|
|`public void setKeyStoreLocation(String keyStoreLocation)`|Java キーストアのファイル名を含む場所を設定します。 keyStoreAuthentication は **JavaKeyStorePassword** を使用して設定する必要があります。|
|`public String getKeyStoreLocation()`|Java キー ストアの keyStoreLocation を取得します。|

### <a name="sqlservercolumnencryptionjavakeystoreprovider-class"></a>SQLServerColumnEncryptionJavaKeyStoreProvider クラス

Java キー ストアにキー ストア プロバイダーを実装します。 このクラスは、Java キー ストアに格納されている証明書を列マスター キーとして使用できるようにします。

**コンストラクター:**

|名前|説明|
|----------|-----------------|
|`public SQLServerColumnEncryptionJavaKeyStoreProvider (String keyStoreLocation, char[] keyStoreSecret)`|Java キー ストアのキー ストア プロバイダー。|

**メソッド:**

|名前|説明|
|----------|-----------------|
|`public byte[] decryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte[] encryptedColumnEncryptionKey)`|列暗号化キーの指定された暗号化値の暗号化を解除します。 暗号化された値は、指定されたキーのパスを持つ証明書と指定されたアルゴリズムを使用して暗号化する必要があります。<br /><br /> **キーのパスは、次のいずれかの形式にする必要があります。**<br /><br /> Thumbprint:<certificate_thumbprint><br /><br /> Alias:<certificate_alias><br /><br /> (`SQLServerColumnEncryptionKeyStoreProvider` をオーバーライドします。 decryptColumnEncryptionKey(String, String, Byte[]) をオーバーライドします。)|
|`public byte[] encryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte[] plainTextColumnEncryptionKey)`|指定されたキーのパスの証明書を使用し、指定されたアルゴリズムを使用して列暗号化キーを暗号化します。<br /><br /> **キーのパスは、次のいずれかの形式にする必要があります。**<br /><br /> Thumbprint:<certificate_thumbprint><br /><br /> Alias:<certificate_alias><br /><br /> (`SQLServerColumnEncryptionKeyStoreProvider` をオーバーライドします。 encryptColumnEncryptionKey(String, String, Byte[]) をオーバーライドします。)|
|`public boolean verifyColumnEncryptionKey (String masterKeyPath, boolean allowEnclaveComputations, byte[] signature)`|証明書を使用して、列暗号化キーの署名を検証します。<br /><br /> **キーのパスは、次のいずれかの形式にする必要があります。**<br /><br /> Thumbprint:<certificate_thumbprint><br /><br /> Alias:<certificate_alias><br /><br /> (`SQLServerColumnEncryptionKeyStoreProvider` をオーバーライドします。 verifyColumnEncryptionKey(String, boolean, Byte[]) をオーバーライドします)。|
|`public void setName (String name)`|このキー ストア プロバイダーの名前を設定します。|
|`public String getName ()`|このキー ストア プロバイダーの名前を取得します。|

### <a name="sqlservercolumnencryptionazurekeyvaultprovider-class"></a>SQLServerColumnEncryptionAzureKeyVaultProvider クラス

Azure Key Vault のキー ストア プロバイダーを実装します。 このクラスを使用すると、Azure Key Vault に格納されているキーを列マスター キーとして使用できます。

**コンストラクター:**

|名前|説明|
|----------|-----------------|
|`public SQLServerColumnEncryptionAzureKeyVaultProvider ()`|Azure Key Vault に対して認証するための SQLServerColumnEncryptionAzureKeyVaultProvider を構築します。|
|`public SQLServerColumnEncryptionAzureKeyVaultProvider (String clientId)`|トークンを要求するクライアントの識別子を使用して、Azure Key Vault に対して認証する SQLServerColumnEncryptionAzureKeyVaultProvider を構築します。|
|`public SQLServerColumnEncryptionAzureKeyVaultProvider (String clientId, String clientKey)`|トークンを要求するクライアントの識別子とキーを使用して、Azure Key Vault に対して認証する SQLServerColumnEncryptionAzureKeyVaultProvider を構築します。|
|`public SQLServerColumnEncryptionAzureKeyVaultProvider (TokenCredential tokenCredential)`|指定された TokenCredential を使用して、Azure Key Vault に対して認証する SQLServerColumnEncryptionAzureKeyVaultProvider を構築します。|

**メソッド:**

|名前|説明|
|----------|-----------------|
|`public byte[] decryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte[] encryptedColumnEncryptionKey)` | 暗号化された列暗号化キー (CEK) の暗号化を解除します。 この暗号化の解除は、マスター キー パスによって指定されている非対称キーを使用する RSA 暗号化アルゴリズムによって行われます。<br />(`SQLServerColumnEncryptionKeyStoreProvider` をオーバーライドします。 decryptColumnEncryptionKey(String, String, Byte[]) をオーバーライドします。) |
| `public byte[] encryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte[] columnEncryptionKey)` | 指定した列マスター キーと指定したアルゴリズムを使用することで、列暗号化キーを暗号化します。<br />(`SQLServerColumnEncryptionKeyStoreProvider` をオーバーライドします。 encryptColumnEncryptionKey(String, String, Byte[]) をオーバーライドします。) |
|`public void setName (String name)`|このキー ストア プロバイダーの名前を設定します。|
|`public String getName ()`|このキー ストア プロバイダーの名前を取得します。|

### <a name="sqlserverkeyvaultauthenticationcallback-interface"></a>SQLServerKeyVaultAuthenticationCallback インターフェイス

このインターフェイスには Azure Key Vault 認証用の 1 つのメソッドが含まれており、ユーザーはこれを実装する必要があります。

**メソッド:**

|名前|説明|
|----------|-----------------|
|`public String getAccessToken(String authority, String resource, String scope);`|このメソッドはオーバーライドする必要があります。 このメソッドは、Azure Key Vault に対するアクセス トークンを取得するために使用されます。|

### <a name="sqlservercolumnencryptionkeystoreprovider-class"></a>SQLServerColumnEncryptionKeyStoreProvider クラス

カスタム キー ストア プロバイダーを実装するには、このクラスを拡張します。

|名前|説明|
|----------|-----------------|
|SQLServerColumnEncryptionKeyStoreProvider|すべてのキー ストア プロバイダーの基底クラス。 カスタム プロバイダーはこのクラスから派生し、そのメンバー関数をオーバーライドしてから、SQLServerConnection.registerColumnEncryptionKeyStoreProviders() を使用して登録する必要があります。 registerColumnEncryptionKeyStoreProviders() を使用して登録する必要があります。|

**メソッド:**

|名前|説明|
|----------|-----------------|
|`public abstract byte[] decryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte [] encryptedColumnEncryptionKey)`|列暗号化キーの暗号化された指定値を暗号化解除するための基本クラス メソッド。 暗号化された値は、指定したキー パスと共に列マスター キーを使用し、指定したアルゴリズムを使用して、暗号化されることが想定されています。|
|`public abstract byte[] encryptColumnEncryptionKey (String masterKeyPath, String encryptionAlgorithm, byte[]  columnEncryptionKey)`|指定したキー パスと共に列マスター キーを使用し、指定したアルゴリズムを使用して、列暗号化キーを暗号化するための基本クラス メソッド。|
|`public abstract void setName(String name)`|このキー ストア プロバイダーの名前を設定します。|
|`public abstract String getName()`|このキー ストア プロバイダーの名前を取得します。|

### <a name="new-or-overloaded-methods-in-sqlserverpreparedstatement-class"></a>SQLServerPreparedStatement クラスの新しいメソッドまたはオーバーロードされたメソッド

|名前|説明|
|----------|-----------------|
|`public void setBigDecimal(int parameterIndex, BigDecimal x, int precision, int scale)`<br /><br /> `public void setObject(int parameterIndex, Object x, int targetSqlType, Integer precision, int scale)`<br /><br /> `public void setObject(int parameterIndex, Object x, SQLType targetSqlType, Integer precision, Integer scale)`<br /><br /> `public void setTime(int parameterIndex, java.sql.Time x, int scale)`<br /><br /> `public void setTimestamp(int parameterIndex, java.sql.Timestamp x, int scale)` <br />`public void setDateTimeOffset(int parameterIndex, microsoft.sql.DateTimeOffset x, int scale)`|これらのメソッドは、Always Encrypted で有効桁数と小数点以下桁数の情報を必要とする特定のデータ型をサポートするため、有効桁数と小数点以下桁数のどちらか一方または両方の引数を使用してオーバーロードされます。|
|`public void setMoney(int parameterIndex, BigDecimal x)`<br /><br /> `public void setSmallMoney(int parameterIndex, BigDecimal x)`<br /><br /> `public void setUniqueIdentifier(int parameterIndex, String guid)`<br /><br /> `public void setDateTime(int parameterIndex, java.sql.Timestamp x)`<br /><br /> `public void setSmallDateTime(int parameterIndex, java.sql.Timestamp x)`|これらのメソッドでは、Always Encrypted での money、smallmoney、uniqueidentifier、datetime、smalldatetime の各データ型のサポートが追加されます。 <br/><br/>暗号化された datetime2 列にパラメーター値を送るために、既存の `setTimestamp()` メソッドが使用されます。 暗号化された datetime および smalldatetime 列については、それぞれ新しいメソッド `setDateTime()` と `setSmallDateTime()` を使用します。|
|`public final void setBigDecimal(int parameterIndex, BigDecimal x, int precision, int scale, boolean forceEncrypt)`<br /><br /> `public final void setMoney(int parameterIndex, BigDecimal x, boolean forceEncrypt)`<br /><br /> `public final void setSmallMoney(int parameterIndex, BigDecimal x, boolean forceEncrypt)`<br /><br /> `public final void setBoolean(int parameterIndex, boolean x, boolean forceEncrypt)`<br /><br /> `public final void setByte(int parameterIndex, byte x, boolean forceEncrypt)`<br /><br /> `public final void setBytes(int parameterIndex, byte x[], boolean forceEncrypt)`<br /><br /> `public final void setUniqueIdentifier(int parameterIndex, String guid, boolean forceEncrypt)`<br /><br /> `public final void setDouble(int parameterIndex, double x, boolean forceEncrypt)`<br /><br /> `public final void setFloat(int parameterIndex, float x, boolean forceEncrypt)`<br /><br /> `public final void setInt(int parameterIndex, int value, boolean forceEncrypt)`<br /><br /> `public final void setLong(int parameterIndex, long x, boolean forceEncrypt)`<br /><br /> `public final setObject(int parameterIndex, Object x, int targetSqlType, Integer precision, int scale, boolean forceEncrypt)`<br /><br /> `public final void setObject(int parameterIndex, Object x, SQLType targetSqlType, Integer precision, Integer scale, boolean forceEncrypt)`<br /><br /> `public final void setShort(int parameterIndex, short x, boolean forceEncrypt)`<br /><br /> `public final void setString(int parameterIndex, String str, boolean forceEncrypt)`<br /><br /> `public final void setNString(int parameterIndex, String value, boolean forceEncrypt)`<br /><br /> `public final void setTime(int parameterIndex, java.sql.Time x, int scale, boolean forceEncrypt)`<br /><br /> `public final void setTimestamp(int parameterIndex, java.sql.Timestamp x, int scale, boolean forceEncrypt)`<br /><br /> `public final void setDateTimeOffset(int parameterIndex, microsoft.sql.DateTimeOffset x, int scale, boolean forceEncrypt)`<br /><br /> `public final void setDateTime(int parameterIndex, java.sql.Timestamp x, boolean forceEncrypt)`<br /><br /> `public final void setSmallDateTime(int parameterIndex, java.sql.Timestamp x, boolean forceEncrypt)`<br /><br /> `public final void setDate(int parameterIndex, java.sql.Date x, java.util.Calendar cal, boolean forceEncrypt)`<br /><br /> `public final void setTime(int parameterIndex, java.sql.Time x, java.util.Calendar cal, boolean forceEncrypt)`<br /><br /> `public final void setTimestamp(int parameterIndex, java.sql.Timestamp x, java.util.Calendar cal, boolean forceEncrypt)`|指定されたパラメーターを、渡された java 値に設定します。<br /><br /> ブール値 `forceEncrypt` が true に設定されている場合、クエリ パラメーターが設定されるのは、指定された列が暗号化され、接続またはステートメントで Always Encrypted が有効になっている場合だけです。<br /><br /> ブール値 forceEncrypt が false に設定されている場合、ドライバーではパラメーターの暗号化は強制されません。|

### <a name="new-or-overloaded-methods-in-sqlservercallablestatement-class"></a>SQLServerCallableStatement クラスの新しいまたはオーバーロードされたメソッド

|名前|説明|
|----------|-----------------|
|`public void registerOutParameter(int parameterIndex, int sqlType, int precision, int scale)`<br /><br /> `public void registerOutParameter(int parameterIndex, SQLType sqlType, int precision, int scale)`<br /><br /> `public void registerOutParameter(String parameterName, int sqlType, int precision, int scale)`<br /><br /> `public void registerOutParameter(String parameterName, SQLType sqlType, int precision, int scale)`<br />`public void setBigDecimal(String parameterName, BigDecimal bd, int precision, int scale)`<br /><br /> `public void setTime(String parameterName, java.sql.Time t, int scale)`<br /><br /> `public void setTimestamp(String parameterName, java.sql.Timestamp t, int scale)`<br /><br /> `public void setDateTimeOffset(String parameterName, microsoft.sql.DateTimeOffset t, int scale)`<br/><br/>`public final void setObject(String sCol, Object x, int targetSqlType, Integer precision, int scale)`|これらのメソッドは、Always Encrypted で有効桁数と小数点以下桁数の情報を必要とする特定のデータ型をサポートするため、有効桁数と小数点以下桁数のどちらか一方または両方の引数を使用してオーバーロードされます。|
|`public void setDateTime(String parameterName, java.sql.Timestamp x)`<br /><br /> `public void setSmallDateTime(String parameterName, java.sql.Timestamp x)`<br /><br /> `public void setUniqueIdentifier(String parameterName, String guid)`<br /><br /> `public void setMoney(String parameterName, BigDecimal bd)`<br /><br /> `public void setSmallMoney(String parameterName, BigDecimal bd)`<br/><br/>`public Timestamp getDateTime(int index)`<br/><br/>`public Timestamp getDateTime(String sCol)`<br/><br/>`public Timestamp getDateTime(int index, Calendar cal)`<br/><br/>`public Timestamp getSmallDateTime(int index)`<br/><br/>`public Timestamp getSmallDateTime(String sCol)`<br/><br/>`public Timestamp getSmallDateTime(int index, Calendar cal)`<br/><br/>`public Timestamp getSmallDateTime(String name, Calendar cal)`<br/><br/>`public BigDecimal getMoney(int index)`<br/><br/>`public BigDecimal getMoney(String sCol)`<br/><br/>`public BigDecimal getSmallMoney(int index)`<br/><br/>`public BigDecimal getSmallMoney(String sCol)`|これらのメソッドでは、Always Encrypted での money、smallmoney、uniqueidentifier、datetime、smalldatetime の各データ型のサポートが追加されます。 <br/><br/>暗号化された datetime2 列にパラメーター値を送るために、既存の `setTimestamp()` メソッドが使用されます。 暗号化された datetime および smalldatetime 列については、それぞれ新しいメソッド `setDateTime()` と `setSmallDateTime()` を使用します。|
|`public void setObject(String parameterName, Object o, int n, int m, boolean forceEncrypt)`<br /><br /> `public void setObject(String parameterName, Object obj, SQLType jdbcType, int scale, boolean forceEncrypt)`<br /><br /> `public void setDate(String parameterName, java.sql.Date x, Calendar c, boolean forceEncrypt)`<br /><br /> `public void setTime(String parameterName, java.sql.Time t, int scale, boolean forceEncrypt)`<br /><br /> `public void setTime(String parameterName, java.sql.Time x, Calendar c, boolean forceEncrypt)`<br /><br /> `public void setDateTime(String parameterName, java.sql.Timestamp x, boolean forceEncrypt)`<br /><br /> `public void setDateTimeOffset(String parameterName, microsoft.sql.DateTimeOffset t, int scale, boolean forceEncrypt)`<br /><br /> `public void setSmallDateTime(String parameterName, java.sql.Timestamp x, boolean forceEncrypt)`<br /><br /> `public void setTimestamp(String parameterName, java.sql.Timestamp t, int scale, boolean forceEncrypt)`<br /><br /> `public void setTimestamp(String parameterName, java.sql.Timestamp x, boolean forceEncrypt)`<br /><br /> `public void setUniqueIdentifier(String parameterName, String guid, boolean forceEncrypt)`<br /><br /> `public void setBytes(String parameterName, byte[] b, boolean forceEncrypt)`<br /><br /> `public void setByte(String parameterName, byte b, boolean forceEncrypt)`<br /><br /> `public void setString(String parameterName, String s, boolean forceEncrypt)`<br /><br /> `public final void setNString(String parameterName, String value, boolean forceEncrypt)<br /><br /> public void setMoney(String parameterName, BigDecimal bd, boolean forceEncrypt)`<br /><br /> `public void setSmallMoney(String parameterName, BigDecimal bd, boolean forceEncrypt)`<br /><br /> `public void setBigDecimal(String parameterName, BigDecimal bd, int precision, int scale, boolean forceEncrypt)`<br /><br /> `public void setDouble(String parameterName, double d, boolean forceEncrypt)`<br /><br /> `public void setFloat(String parameterName, float f, boolean forceEncrypt)`<br /><br /> `public void setInt(String parameterName, int i, boolean forceEncrypt)`<br /><br /> `public void setLong(String parameterName, long l, boolean forceEncrypt)`<br /><br /> `public void setShort(String parameterName, short s, boolean forceEncrypt)`<br /><br /> `public void setBoolean(String parameterNames, boolean b, boolean forceEncrypt)`<br/><br/>`public void setTimeStamp(String sCol, java.sql.Timestamp x, Calendar c, Boolean forceEncrypt)`|指定されたパラメーターを、渡された java 値に設定します。<br /><br /> ブール値 forceEncrypt が true に設定されている場合、クエリ パラメーターが設定されるのは、指定された列が暗号化され、接続またはステートメントで Always Encrypted が有効になっている場合だけです。<br /><br /> ブール値 forceEncrypt が false に設定されている場合、ドライバーではパラメーターの暗号化は強制されません。|

### <a name="new-or-overloaded-methods-in-sqlserverresultset-class"></a>SQLServerResultSet クラスの新しいまたはオーバーロードされたメソッド

|名前|説明|
|----------|-----------------|
|`public String getUniqueIdentifier(int columnIndex)`<br/><br/>`public String getUniqueIdentifier(String columnLabel)`<br/><br/>   `public java.sql.Timestamp getDateTime(int columnIndex)` <br/><br/> `public java.sql.Timestamp getDateTime(String columnName)`   <br/><br/> `public java.sql.Timestamp getDateTime(int columnIndex, Calendar cal)`   <br/><br/>`public java.sql.Timestamp getDateTime(String colName, Calendar cal)`    <br/><br/>`public java.sql.Timestamp getSmallDateTime(int columnIndex)`    <br/><br/> `public java.sql.Timestamp getSmallDateTime(String columnName)`   <br/><br/> `public java.sql.Timestamp getSmallDateTime(int columnIndex, Calendar cal)`   <br/><br/> `public java.sql.Timestamp getSmallDateTime(String colName, Calendar cal)`   <br/><br/>  `public BigDecimal getMoney(int columnIndex)`  <br/><br/> `public BigDecimal getMoney(String columnName)`   <br/><br/> `public BigDecimal getSmallMoney(int columnIndex)`   <br/><br/>  `public BigDecimal getSmallMoney(String columnName)`  <br/><br/>`public void updateMoney(String columnName, BigDecimal x)`    <br/><br/>  `public void updateSmallMoney(String columnName, BigDecimal x)`  <br/><br/>     `public void updateDateTime(int index, java.sql.Timestamp x)` <br/><br/> `public void updateSmallDateTime(int index, java.sql.Timestamp x)` |これらのメソッドでは、Always Encrypted での money、smallmoney、uniqueidentifier、datetime、smalldatetime の各データ型のサポートが追加されます。 <br/><br/>暗号化された datetime2 列を更新するために、既存の `updateTimestamp()` メソッドが使用されます。 暗号化された datetime および smalldatetime 列については、それぞれ新しいメソッド `updateDateTime()` と `updateSmallDateTime()` を使用します。|
|`public void updateBoolean(int index, boolean x, boolean forceEncrypt)`  <br/><br/>  `public void updateByte(int index, byte x, boolean forceEncrypt)`  <br/><br/>  `public void updateShort(int index, short x, boolean forceEncrypt)`  <br/><br/> `public void updateInt(int index, int x, boolean forceEncrypt)`   <br/><br/>  `public void updateLong(int index, long x, boolean forceEncrypt)`  <br/><br/> `public void updateFloat(int index, float x, boolean forceEncrypt)`   <br/><br/> `public void updateDouble(int index, double x, boolean forceEncrypt)`   <br/><br/> `public void updateMoney(int index, BigDecimal x, boolean forceEncrypt)`   <br/><br/>  `public void updateMoney(String columnName, BigDecimal x, boolean forceEncrypt)`  <br/><br/> `public void updateSmallMoney(int index, BigDecimal x, boolean forceEncrypt)`   <br/><br/>  `public void updateSmallMoney(String columnName, BigDecimal x, boolean forceEncrypt)`  <br/><br/> `public void updateBigDecimal(int index, BigDecimal x, Integer precision, Integer scale, boolean forceEncrypt)`   <br/><br/>  `public void updateString(int columnIndex, String stringValue, boolean forceEncrypt)`  <br/><br/>  `public void updateNString(int columnIndex, String nString, boolean forceEncrypt)`  <br/><br/>  `public void updateNString(String columnLabel, String nString, boolean forceEncrypt)`  <br/><br/> `public void updateBytes(int index, byte x[], boolean forceEncrypt)   <br/><br/>  public void updateDate(int index, java.sql.Date x, boolean forceEncrypt)`  <br/><br/> `public void updateTime(int index, java.sql.Time x, Integer scale, boolean forceEncrypt)`   <br/><br/> `public void updateTimestamp(int index, java.sql.Timestamp x, int scale, boolean forceEncrypt)`   <br/><br/> `public void updateDateTime(int index, java.sql.Timestamp x, Integer scale, boolean forceEncrypt)`   <br/><br/> `public void updateSmallDateTime(int index, java.sql.Timestamp x, Integer scale, boolean forceEncrypt)`   <br/><br/>  `public void updateDateTimeOffset(int index, microsoft.sql.DateTimeOffset x, Integer scale, boolean forceEncrypt)`  <br/><br/> `public void updateUniqueIdentifier(int index, String x, boolean forceEncrypt)`    <br/><br/>  `public void updateObject(int index, Object x, int precision, int scale, boolean forceEncrypt)`  <br/><br/>  `public void updateObject(int index, Object obj, SQLType targetSqlType, int scale, boolean forceEncrypt)`  <br/><br/> `public void updateBoolean(String columnName, boolean x, boolean forceEncrypt)`    <br/><br/>  `public void updateByte(String columnName, byte x, boolean forceEncrypt)`  <br/><br/>  `public void updateShort(String columnName, short x, boolean forceEncrypt)`  <br/><br/> `public void updateInt(String columnName, int x, boolean forceEncrypt)`   <br/><br/>   `public void updateLong(String columnName, long x, boolean forceEncrypt)` <br/><br/>  `public void updateFloat(String columnName, float x, boolean forceEncrypt)`  <br/><br/>  `public void updateDouble(String columnName, double x, boolean forceEncrypt)  <br/><br/> public void updateBigDecimal(String columnName, BigDecimal x, boolean forceEncrypt)`   <br/><br/>  `public void updateBigDecimal(String columnName, BigDecimal x, Integer precision, Integer scale, boolean forceEncrypt)`  <br/><br/> `public void updateString(String columnName, String x, boolean forceEncrypt)`   <br/><br/>  `public void updateBytes(String columnName, byte x[], boolean forceEncrypt)`  <br/><br/> `public void updateDate(String columnName, java.sql.Date x, boolean forceEncrypt)`   <br/><br/>  `public void updateTime(String columnName, java.sql.Time x, int scale, boolean forceEncrypt)`  <br/><br/>  `public void updateTimestamp(String columnName, java.sql.Timestamp x, int scale, boolean forceEncrypt)`  <br/><br/> `public void updateDateTime(String columnName, java.sql.Timestamp x, int scale, boolean forceEncrypt)`   <br/><br/>  `public void updateSmallDateTime(String columnName, java.sql.Timestamp x, int scale, boolean forceEncrypt)`  <br/><br/>  `public void updateDateTimeOffset(String columnName, microsoft.sql.DateTimeOffset x, int scale, boolean forceEncrypt)`  <br/><br/>  `public void updateUniqueIdentifier(String columnName, String x, boolean forceEncrypt)`<br/><br/>`public void updateObject(String columnName, Object x, int precision, int scale, boolean forceEncrypt)`<br/><br/>`public void updateObject(String columnName, Object obj, SQLType targetSqlType, int scale, boolean forceEncrypt)`|指定された列を、渡された Java 値に更新します。<br/><br/>ブール値 `forceEncrypt` が true に設定されている場合、列が設定されるのは、列が暗号化され、接続またはステートメントで Always Encrypted が有効になっている場合だけです。<br/><br/>ブール値 `forceEncrypt` が false に設定されている場合、ドライバーによってパラメーターの暗号化が強制的に実行されることはありません。|

### <a name="new-types-in-microsoftsqltypes-class"></a>microsoft.sql.Types クラスの新しい型

|名前|説明|
|----------|-----------------|
|DATETIME、SMALLDATETIME、MONEY、SMALLMONEY、GUID|`setObject()/updateObject()` API メソッドを使用して **暗号化された** datetime、smalldatetime、money、smallmoney、uniqueidentifier 列にパラメーター値を送信するときは、これらの型をターゲット SQL 型として使用します。|

### <a name="sqlserverstatementcolumnencryptionsetting-enum"></a>SQLServerStatementColumnEncryptionSetting Enum

暗号化された列を読み書きするときにデータを送受信する方法を指定します。 クエリによっては、暗号化されていない列を使用する場合に Always Encrypted ドライバーの処理をバイパスすることにより、パフォーマンスの影響が軽減される可能性があります。 暗号化を回避したり、プレーンテキスト データにアクセスしたりするためにこれらの設定値を使用することはできません。

**構文 :**

```java
Public enum  SQLServerStatementColumnEncryptionSetting
```

**メンバー:**

|名前|説明|
|----------|-----------------|
|UseConnectionSetting|接続文字列で、コマンドが Always Encrypted 設定を既定として設定するように指定します。|
|Enabled|クエリで Always Encrypted を有効にします。|
|ResultSetOnly|ドライバーでコマンドの結果だけを Always Encrypted ルーチンで処理するよう指定します。 コマンドに暗号化が必要なパラメーターがない場合にこの値を使用します。|
|無効|クエリで Always Encrypted を無効にします。|

AE のステートメント レベルの設定は、SQLServerConnection クラスと SQLServerConnectionPoolProxy クラスに追加されます。 これらのクラスの次のメソッドは、新しい設定でオーバーロードされます。

|名前|説明|
|----------|-----------------|
|`public Statement createStatement(int nType, int nConcur, int statementHoldability, SQLServerStatementColumnEncryptionSetting stmtColEncSetting)`|指定された型、コンカレンシー、保持可能性、列暗号化の設定を持つ ResultSet オブジェクトを生成する Statement オブジェクトを作成します。|
|`public CallableStatement prepareCall(String sql, int nType, int nConcur, int statementHoldability, SQLServerStatementColumnEncryptionSetting stmtColEncSetiing)`|指定された型、コンカレンシー、保持可能性を持つ ResultSet オブジェクトを生成する CallableStatement オブジェクトを、指定された列暗号化設定を使用して作成します。|
|`public PreparedStatement prepareStatement(String sql, int autogeneratedKeys, SQLServerStatementColumnEncryptionSetting stmtColEncSetting)`|自動生成されたキーを取得する機能を持つ PreparedStatement オブジェクトを、指定された列暗号化設定を使用して作成します。|
|`public PreparedStatement prepareStatement(String sql, String[] columnNames, SQLServerStatementColumnEncryptionSetting stmtColEncSetting)`|指定された列名を持つ ResultSet オブジェクトを生成する PreparedStatement オブジェクトを、指定された列暗号化設定を使用して作成します。|
|`public PreparedStatement prepareStatement(String sql, int[] columnIndexes, SQLServerStatementColumnEncryptionSetting stmtColEncSetting`|指定された列インデックスを持つ ResultSet オブジェクトを生成する PreparedStatement オブジェクトを、指定された列暗号化設定を使用して作成します。|
|`public PreparedStatement prepareStatement(String sql, int nType, int nConcur, int nHold, SQLServerStatementColumnEncryptionSetting stmtColEncSetting)`|指定された型、コンカレンシー、保持可能性を持つ ResultSet オブジェクトを生成する PreparedStatement オブジェクトを、指定された列暗号化設定を使用して作成します。|

> [!NOTE]
> クエリに対して Always Encrypted が無効になっていて、クエリに暗号化が必要なパラメーター (暗号化された列に対応するパラメーター) ある場合、クエリは失敗します。
>
> クエリに対して Always Encrypted が無効になっていて、クエリで暗号化された列からの結果が返される場合、クエリでは暗号化された値が返されます。 暗号化された値のデータ型は varbinary になります。

## <a name="see-also"></a>関連項目

[JDBC ドライバーでの Always Encrypted の使用](using-always-encrypted-with-the-jdbc-driver.md)
