<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="c3b37-101">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c3b37-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="c3b37-102">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c3b37-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="c3b37-103">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="c3b37-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="c3b37-104">`[DataType(DataType.Date)]`:[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (Date) を指定します。</span><span class="sxs-lookup"><span data-stu-id="c3b37-104">`[DataType(DataType.Date)]`:  The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="c3b37-105">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="c3b37-105">With this attribute:</span></span>

  * <span data-ttu-id="c3b37-106">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c3b37-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c3b37-107">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="c3b37-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c3b37-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="c3b37-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
