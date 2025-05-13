---
title: Azure AI Document Intelligence の演習
permalink: index.html
layout: home
---

次の演習は、Microsoft Azure でドキュメント インテリジェンス ソリューションを構築するときに開発者が行う一般的なタスクについて調べる、実践的な学習エクスペリエンスを提供するように設計されています。

> **注**: これらの演習を完了するには、必要な Azure リソースをプロビジョニングするのに十分なアクセス許可とクォータがある Azure サブスクリプションが必要です。 まだお持ちでない場合は、[Azure アカウント](https://azure.microsoft.com/free)にサインアップできます。 新規ユーザーには、最初の 30 日間のクレジットが付属する無料試用版オプションがあります。

## 演習

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %} {% if activity.url contains 'ai-foundry' %} {% continue %} {% endif %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})

{{activity.lab.description}}

{% endfor %}

> **注**: これらの演習は単独でも完了できますが、[Microsoft Learn](https://learn.microsoft.com/training/paths/extract-data-from-forms-document-intelligence/) のモジュールを補完するように設計されています。このモジュールでは、これらの演習の基になる概念の一部について詳しく説明しています。
