---
title: Swagger/OpenAPI を使用する ASP.NET Core Web API のドキュメント
author: RicoSuter
description: このチュートリアルでは、Swagger を追加して、Web API アプリのドキュメントとヘルプ ページを生成する手順を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062455"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="20645-103">Swagger/OpenAPI を使用する ASP.NET Core Web API のドキュメント</span><span class="sxs-lookup"><span data-stu-id="20645-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="20645-104">作成者: [Christoph Nienaber](https://twitter.com/zuckerthoben) および [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="20645-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="20645-105">Swagger (OpenAPI) は REST API を記述するための仕様であり、言語に依存しません。</span><span class="sxs-lookup"><span data-stu-id="20645-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="20645-106">これにより、コンピューターと人間の両方が、ソース コードに直接アクセスせずに REST API の機能を理解できるようになります。</span><span class="sxs-lookup"><span data-stu-id="20645-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="20645-107">主な目的は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="20645-107">Its main goals are to:</span></span>

* <span data-ttu-id="20645-108">関連のないサービスに接続するために必要な作業量を最小限にします。</span><span class="sxs-lookup"><span data-stu-id="20645-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="20645-109">正確にサービスをドキュメント化するために必要な時間を減らします。</span><span class="sxs-lookup"><span data-stu-id="20645-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="20645-110">.NET 用の 2 つの主要な OpenAPI の実装は、[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) と [NSwag](https://github.com/RicoSuter/NSwag) です。次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20645-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="20645-111">Swashbuckle の概要</span><span class="sxs-lookup"><span data-stu-id="20645-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="20645-112">NSwag の概要</span><span class="sxs-lookup"><span data-stu-id="20645-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="20645-113">OpenApi と Swagger</span><span class="sxs-lookup"><span data-stu-id="20645-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="20645-114">Swagger プロジェクトは 2015 年に OpenAPI Initiative に寄贈され、それ以降は OpenAPI と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="20645-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="20645-115">どちらの名前も同じように使用されます。</span><span class="sxs-lookup"><span data-stu-id="20645-115">Both names are used interchangeably.</span></span> <span data-ttu-id="20645-116">ただし、"OpenAPI" は仕様を指します。</span><span class="sxs-lookup"><span data-stu-id="20645-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="20645-117">"Swagger" は、オープンソース製品および OpenAPI Specification と協力している SmartBear による商用製品のファミリを指します。</span><span class="sxs-lookup"><span data-stu-id="20645-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="20645-118">[OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator) などの後続のオープンソース製品も Swagger ファミリ名に該当しますが、SmartBear からはリリースされていません。</span><span class="sxs-lookup"><span data-stu-id="20645-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="20645-119">要約すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="20645-119">In short:</span></span>

* <span data-ttu-id="20645-120">OpenAPI は仕様です。</span><span class="sxs-lookup"><span data-stu-id="20645-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="20645-121">Swagger は、OpenAPI 仕様を使用するツールです。</span><span class="sxs-lookup"><span data-stu-id="20645-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="20645-122">たとえば、OpenAPIGenerator や SwaggerUI などです。</span><span class="sxs-lookup"><span data-stu-id="20645-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="20645-123">OpenAPI の仕様 (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="20645-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="20645-124">OpenAPI の仕様は、API の機能について説明されているドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="20645-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="20645-125">そのドキュメントは、コントローラーとモデル内の XML および属性の注釈に基づいています。</span><span class="sxs-lookup"><span data-stu-id="20645-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="20645-126">それは OpenAPI フローの中核部分であり、SwaggerUI などのツールを駆動するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="20645-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="20645-127">既定では、 *openapi.json* という名前になっています。</span><span class="sxs-lookup"><span data-stu-id="20645-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="20645-128">簡略化された OpenAPI の仕様の例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="20645-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="20645-129">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="20645-129">Swagger UI</span></span>

<span data-ttu-id="20645-130">[Swagger UI](https://swagger.io/swagger-ui/) には、生成された OpenAPI の仕様を使用してサービスに関する情報が提供される Web ベースの UI が用意されています。</span><span class="sxs-lookup"><span data-stu-id="20645-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="20645-131">ミドルウェアの登録呼び出しを使用して ASP.NET Core アプリでホストすることができるように、Swashbuckle と NSwag の両方に埋め込みバージョンの Swagger UI が含まれます。</span><span class="sxs-lookup"><span data-stu-id="20645-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="20645-132">Web UI は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="20645-132">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="20645-134">コントローラー内の各パブリック アクション メソッドを UI からテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="20645-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="20645-135">メソッドの名前を選択してセクションを展開します。</span><span class="sxs-lookup"><span data-stu-id="20645-135">Select a method name to expand the section.</span></span> <span data-ttu-id="20645-136">必要なパラメーターを追加し、 **[Try it out!]\(試してみる\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20645-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Swagger GET テストの例](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="20645-138">スクリーンショットに使用される Swagger UI バージョンは、バージョン 2 です。</span><span class="sxs-lookup"><span data-stu-id="20645-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="20645-139">バージョン 3 の例については、[Petstore の例](https://petstore.swagger.io/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20645-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="20645-140">次の手順</span><span class="sxs-lookup"><span data-stu-id="20645-140">Next steps</span></span>

* [<span data-ttu-id="20645-141">Swashbuckle の概要</span><span class="sxs-lookup"><span data-stu-id="20645-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="20645-142">NSWag の概要</span><span class="sxs-lookup"><span data-stu-id="20645-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
