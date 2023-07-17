# secretをgit管理できるようにする
secretの値はbase64でエンコードされているだけなので、簡単にdecodeができる。
つまり、GitHubのPATとかをリモートリポジトリに上げるのはご法度。
ただ、SecretをGitHub管理できたほうが便利なのは間違いない。
そのために[こんなソフトウェア](https://developers.cyberagent.co.jp/blog/archives/40766/)がある。
この辺のソフトウェアを導入してsecretをgit管理できるようにしたい。

# システム監視できるようにする
以下の監視をできるようにする。
- アプリケーション
- データベース
- OS

監視等は最もよく見かけるPrometheusを使う。
いったん、この本を読む。
- https://learning.oreilly.com/library/view/prometheus-up/9781098131135/

# アプリケーションを動かす
[このサンプルアプリ](https://github.com/koheiyamayama/ks-laboratory-backend)をMySQLで動かす。
ks-laboratory-cluster(kohei's laboratory cluster)でサンプルアプリとMySQLを起動して動かす。
ks-laboratory-backendを動かすためにghcrにprivate packageとしてdocker imageをpushする。
private repoからdocker imageを取得する方法を調べて、実施する。
https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
kubectl create secret docker-registryコマンドでsecretを作成し、deploymentのmanifestでimagePullSecretsを指定すればいい感じにできる。

MySQLをk8sで動かすためにVolumeやStatefulSet?辺りが必要そうなので、その辺りのドキュメントを読む。
- [複数のMySQL Instanceを起動する](https://kubernetes.io/ja/docs/tasks/run-application/run-replicated-stateful-application/)
- [単一のMySQL Instanceを起動する](https://kubernetes.io/ja/docs/tasks/run-application/run-single-instance-stateful-application/)

いったんStatefulSetでリードレプリカ構成を作るのはやめて、PV, PVCを使ったシングルインスタンスでMySQLを運用する。
リードレプリカ構成するにはk8sの知識より、MySQLの知識や運用経験が求められるので、そっちを学ぶ。

# MetalLBを使ったNginxコンテナの公開
[これ](https://metallb.universe.tf/installation/)を使ってMetalLBをインストールする。
[この記事](https://zenn.dev/vampire_yuta/articles/ccbc57be8e092a)がすごい参考になった。


# NodePortを使ったNginxコンテナの公開
そのまま。NginxコンテナをDeploymentを使って3つ起動する。
そのDeploymentをServiceType NodePortで公開する。
