---
title: Swagger / OpenAPI を使用する ASP.NET Core Web API のヘルプ ページ
author: RicoSuter
description: このチュートリアルでは、Swagger を追加して、Web API アプリのドキュメントとヘルプ ページを生成する手順を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021979"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Swagger/OpenAPI を使用する ASP.NET Core Web API のヘルプ ページ

作成者: [Christoph Nienaber](https://twitter.com/zuckerthoben) および [Rico Suter](https://blog.rsuter.com/)

Web API を使用する場合、さまざまなメソッドを理解することは開発者にとって困難な場合があります。 [Swagger](https://swagger.io/) ([OpenAPI](https://www.openapis.org/) とも呼ばれる) では、Web API の役立つドキュメントとヘルプ ページの生成に関する問題が解決されます。 Swagger では、対話型のドキュメント、クライアント SDK の生成、API の発見可能性などの利点を提供します。

この記事では、[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) と [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger の実装を示します。

* **Swashbuckle.AspNetCore** は、ASP.NET Core Web API の Swagger ドキュメントを生成するためのオープン ソース プロジェクトです。

* **NSwag** は、Swagger ドキュメントを生成し、[Swagger UI](https://swagger.io/swagger-ui/) や [ReDoc](https://github.com/Rebilly/ReDoc) を ASP.NET Core Web API に統合するためのもう 1 つのオープン ソース プロジェクトです。 また、NSwag によって、API 用に C# と TypeScript のクライアント コードを生成するための手段が提供されます。

## <a name="what-is-swagger--openapi"></a>Swagger / OpenAPI とは

Swagger は、[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API を記述するために、言語に関係なく使える仕様です。 Swagger プロジェクトは、現在、OpenAPI と呼ばれている [OpenAPI イニシアティブ](https://www.openapis.org/)に寄贈されました。 どちらの名前も同様に使用できますが、OpenAPI の使用をお勧めします。 Open API では、実装 (ソース コード、ネットワーク アクセス、ドキュメント) に直接アクセスすることなく、コンピューターと人間の両方がサービスの機能を理解できます。 関連付けられていないサービスに接続するために必要な作業量を最小限にすることが 1 つの目標です。 もう 1 つの目標は、正確にサービスをドキュメント化するために必要な時間を減らすことです。

## <a name="openapi-specification-openapijson"></a>OpenAPI の仕様 (openapi.json)

OpenAPI フローの基本は、OpenAPI の仕様です。既定では、ドキュメントの名前は *openapi.json* です。 それは、サービスに基づいて OpenAPI ツール チェーン (またはサード パーティによるその実装) によって生成されます。 ここでは API の機能と HTTP を使用してアクセスする方法について説明します。 これにより Swagger UI が駆動され、検出とクライアント コードの生成が行えるように、ツール チェーンによって使用されます。 簡略化された OpenAPI の仕様の例は次のとおりです。

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Swagger UI

[Swagger UI](https://swagger.io/swagger-ui/) には、生成された OpenAPI の仕様を使用してサービスに関する情報が提供される Web ベースの UI が用意されています。 ミドルウェアの登録呼び出しを使用して ASP.NET Core アプリでホストすることができるように、Swashbuckle と NSwag の両方に埋め込みバージョンの Swagger UI が含まれます。 Web UI は次のようになります。

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

コントローラー内の各パブリック アクション メソッドを UI からテストすることができます。 メソッドの名前をクリックし、セクションを展開します。 任意の必要なパラメーターを追加し、 **[Try it out!]** をクリックします。

![Swagger GET テストの例](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> スクリーンショットに使用される Swagger UI バージョンは、バージョン 2 です。 バージョン 3 の例については、[Petstore の例](https://petstore.swagger.io/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Swashbuckle の概要](xref:tutorials/get-started-with-swashbuckle)
* [NSWag の概要](xref:tutorials/get-started-with-nswag)
