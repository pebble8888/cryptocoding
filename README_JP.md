#暗号プログラミング

* 秘密の文字列を定数時間で比較する

効率が良い比較ロジックだとその関数呼び出しにかかった時間を計測することで,
どの程度一致していたかを知ることができ,容易に秘密の文字列を特定できてしまう。

攻撃手法:タイミングアタック
C の memcmp, Java の Arrays.equals, Python の == は定数時間ではないので使わない。
自前で定数時間比較関数を作りそれを用いる。
OpenSSL では `CRYPTO_memcmp` を用いる。
Python 2.7.7 以降の `hmac.compare_digest` を用いる。
Java では `java.security.MessageDigest.isEqual` を用いる。
Go では `crypto/subtle` を用いる。

* 秘密データに応じた分岐を避ける

攻撃手法:タイミングアタック
分岐により処理時間が変わる。

* 秘密データでインデックスされた検索テーブルを避ける

攻撃手法:タイミングアタック

* 秘密に依存するループ境界を避ける

攻撃手法:タイミングアタック

* セキュリティ的に重要な操作にコンパイラが干渉するのを避ける

memset など不要な処理をコンパイラが省略することがある。

対応: C11 では最適化されない `memset_s` が導入された。

* 安全と安全でないAPIの混同を避ける

多くのプログラミング環境で、ほとんど同じ機能だがセキュリティ属性が異なるAPIが容易されている。
例えば, OpenSSL は `RAND_bytes()` と `RAND_pseudo_bytes()` をもつ。
BSD C ライブラリは `random()` と `arc4random() をもつ。
Java は `Random` と `SecureRandom` をもつ。

安全な関数に安全でない変数を含めない。

* 同じAPI階層で安全確保と暗号的要素の抽象度を混ぜるのを避ける

* バイナリデータを表現するのに unsigned bytes を使用する

* 秘密データのメモリをクリアする

スコープを抜ける際に秘密データを含む全ての変数をクリアする。

win32 では `SecureZeroMemory()`, OpenSSL では `OPENSSL_cleanse()` のような関数がある。 

* 強いランダム性を用いる

* タイムスタンプやプロセスID,温度センサーのような予測可能なエントロピーに依存しない。
* 一般用途として擬似ランダム関数 `stdlib` の `rand()`, `srand()`, `random()`, Python の `random` モジュールに依存しない。
* [Mersenne Twister] を用いない。

Linux では `getrandom()` を用いる。
OpenBSB では `getentropy()` を用いる。
OpenSSL API では `RAND_bytes()` を用いる。
Windows では `rand_s` を用いる。
最近の Windows プラットフォームでは, BCryptGenRandom を使う。

* 常にタイプキャストされたシフト値を用いる

C言語において、左シフトはシフトされた値が符号ありの場合,未定義動作である。

