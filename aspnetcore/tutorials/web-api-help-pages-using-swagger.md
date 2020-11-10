---
title: Swagger/OpenAPI を使用する ASP.NET Core Web API のドキュメント
author: RicoSuter
description: このチュートリアルでは、Swagger を追加して、Web API アプリのドキュメントとヘルプ ページを生成する手順を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062455"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Swagger/OpenAPI を使用する ASP.NET Core Web API のドキュメント

作成者: [Christoph Nienaber](https://twitter.com/zuckerthoben) および [Rico Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) は REST API を記述するための仕様であり、言語に依存しません。 これにより、コンピューターと人間の両方が、ソース コードに直接アクセスせずに REST API の機能を理解できるようになります。 主な目的は次のとおりです。

* 関連のないサービスに接続するために必要な作業量を最小限にします。
* 正確にサービスをドキュメント化するために必要な時間を減らします。

.NET 用の 2 つの主要な OpenAPI の実装は、[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) と [NSwag](https://github.com/RicoSuter/NSwag) です。次を参照してください。

* [Swashbuckle の概要](xref:tutorials/get-started-with-swashbuckle)
* [NSwag の概要](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi と Swagger

Swagger プロジェクトは 2015 年に OpenAPI Initiative に寄贈され、それ以降は OpenAPI と呼ばれています。 どちらの名前も同じように使用されます。 ただし、"OpenAPI" は仕様を指します。 "Swagger" は、オープンソース製品および OpenAPI Specification と協力している SmartBear による商用製品のファミリを指します。 [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator) などの後続のオープンソース製品も Swagger ファミリ名に該当しますが、SmartBear からはリリースされていません。

要約すると、次のようになります。

* OpenAPI は仕様です。
* Swagger は、OpenAPI 仕様を使用するツールです。 たとえば、OpenAPIGenerator や SwaggerUI などです。

## <a name="openapi-specification-openapijson"></a>OpenAPI の仕様 (openapi.json)

OpenAPI の仕様は、API の機能について説明されているドキュメントです。 そのドキュメントは、コントローラーとモデル内の XML および属性の注釈に基づいています。 それは OpenAPI フローの中核部分であり、SwaggerUI などのツールを駆動するために使用されます。 既定では、 *openapi.json* という名前になっています。 簡略化された OpenAPI の仕様の例は次のとおりです。

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

コントローラー内の各パブリック アクション メソッドを UI からテストすることができます。 メソッドの名前を選択してセクションを展開します。 必要なパラメーターを追加し、 **[Try it out!]\(試してみる\)** を選択します。

![Swagger GET テストの例](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> スクリーンショットに使用される Swagger UI バージョンは、バージョン 2 です。 バージョン 3 の例については、[Petstore の例](https://petstore.swagger.io/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Swashbuckle の概要](xref:tutorials/get-started-with-swashbuckle)
* [NSWag の概要](xref:tutorials/get-started-with-nswag)
