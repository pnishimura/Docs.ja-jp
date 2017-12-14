---
uid: web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-6
title: "データベースの概要 Entity Framework 4.0 最初および ASP.NET 4 Web フォームの第 6 部 |Microsoft ドキュメント"
author: tdykstra
description: "Contoso 大学でサンプル web アプリケーションでは、Entity Framework を使用して ASP.NET Web フォーム アプリケーションを作成する方法を示します。 サンプル アプリケーションは、."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/03/2010
ms.topic: article
ms.assetid: 994a5496-c648-4830-b03c-55bb43f325d2
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-6
msc.type: authoredcontent
ms.openlocfilehash: 164c2002a119420555d2c7065c5a79a5f433a725
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
<a name="getting-started-with-entity-framework-40-database-first-and-aspnet-4-web-forms---part-6"></a><span data-ttu-id="c8db5-104">データベースの概要 Entity Framework 4.0 最初および ASP.NET 4 Web フォームの第 6 部</span><span class="sxs-lookup"><span data-stu-id="c8db5-104">Getting Started with Entity Framework 4.0 Database First and ASP.NET 4 Web Forms - Part 6</span></span>
====================
<span data-ttu-id="c8db5-105">によって[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c8db5-105">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

> <span data-ttu-id="c8db5-106">Contoso 大学でサンプル web アプリケーションでは、Entity Framework 4.0 および Visual Studio 2010 を使用して ASP.NET Web フォーム アプリケーションを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-106">The Contoso University sample web application demonstrates how to create ASP.NET Web Forms applications using the Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="c8db5-107">一連のチュートリアルについては、次を参照してください[系列内の最初のチュートリアル。](the-entity-framework-and-aspnet-getting-started-part-1.md)</span><span class="sxs-lookup"><span data-stu-id="c8db5-107">For information about the tutorial series, see [the first tutorial in the series](the-entity-framework-and-aspnet-getting-started-part-1.md)</span></span>


## <a name="implementing-table-per-hierarchy-inheritance"></a><span data-ttu-id="c8db5-108">Table-Per-Hierarchy 継承の実装</span><span class="sxs-lookup"><span data-stu-id="c8db5-108">Implementing Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="c8db5-109">前のチュートリアルで使用していた関連するデータを追加してリレーションシップを削除して、既存のエンティティとの関係のある新しいエンティティを追加することによりします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-109">In the previous tutorial you worked with related data by adding and deleting relationships and by adding a new entity that had a relationship to an existing entity.</span></span> <span data-ttu-id="c8db5-110">このチュートリアルでは、データ モデルでの継承を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-110">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="c8db5-111">オブジェクト指向プログラミングでは、関連するクラスを使用しやすく継承を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8db5-111">In object-oriented programming, you can use inheritance to make it easier to work with related classes.</span></span> <span data-ttu-id="c8db5-112">たとえば、作成した`Instructor`と`Student`から派生したクラス、`Person`基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-112">For example, you could create `Instructor` and `Student` classes that derive from a `Person` base class.</span></span> <span data-ttu-id="c8db5-113">Entity Framework では、同じ種類のエンティティ間の継承構造を作成できます。</span><span class="sxs-lookup"><span data-stu-id="c8db5-113">You can create the same kinds of inheritance structures among entities in the Entity Framework.</span></span>

<span data-ttu-id="c8db5-114">このチュートリアルでは、新しい web ページを作成できません。</span><span class="sxs-lookup"><span data-stu-id="c8db5-114">In this part of the tutorial, you won't create any new web pages.</span></span> <span data-ttu-id="c8db5-115">代わりに、データ モデルにエンティティを派生を追加して、新しいエンティティを使用して既存のページを変更します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-115">Instead, you'll add derived entities to the data model and modify existing pages to use the new entities.</span></span>

## <a name="table-per-hierarchy-versus-table-per-type-inheritance"></a><span data-ttu-id="c8db5-116">テーブルの種類ごとの継承ではなくテーブルは階層ごと</span><span class="sxs-lookup"><span data-stu-id="c8db5-116">Table-per-Hierarchy versus Table-per-Type Inheritance</span></span>

<span data-ttu-id="c8db5-117">データベースは、1 つのテーブルまたは複数のテーブルに関連するオブジェクトに関する情報を格納できます。</span><span class="sxs-lookup"><span data-stu-id="c8db5-117">A database can store information about related objects in one table or in multiple tables.</span></span> <span data-ttu-id="c8db5-118">たとえば、`School`データベース、`Person`テーブルには、受講者と講習においてインストラクターに 1 つのテーブルの両方の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8db5-118">For example, in the `School` database, the `Person` table includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="c8db5-119">講習においてインストラクターにのみ適用の一部の列 (`HireDate`)、受講者にのみ一部 (`EnrollmentDate`)、および両方にいくつか (`LastName`、 `FirstName`)。</span><span class="sxs-lookup"><span data-stu-id="c8db5-119">Some of the columns apply only to instructors (`HireDate`), some only to students (`EnrollmentDate`), and some to both (`LastName`, `FirstName`).</span></span>

<span data-ttu-id="c8db5-120">[![image11](the-entity-framework-and-aspnet-getting-started-part-6/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-120">[![image11](the-entity-framework-and-aspnet-getting-started-part-6/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image1.png)</span></span>

<span data-ttu-id="c8db5-121">作成するエンティティ フレームワークを構成することができます`Instructor`と`Student`から継承するエンティティ、`Person`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-121">You can configure the Entity Framework to create `Instructor` and `Student` entities that inherit from the `Person` entity.</span></span> <span data-ttu-id="c8db5-122">1 つのデータベース テーブルからエンティティ継承構造を生成するには、このパターンが呼び出された*テーブルの階層あたり*(TPH) 継承します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-122">This pattern of generating an entity inheritance structure from a single database table is called *table-per-hierarchy* (TPH) inheritance.</span></span>

<span data-ttu-id="c8db5-123">コース、`School`データベースは、さまざまなパターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-123">For courses, the `School` database uses a different pattern.</span></span> <span data-ttu-id="c8db5-124">オンライン コースとオンサイト コースがそれぞれを指す外部キーを持つ個別のテーブルに格納されている、`Course`テーブル。</span><span class="sxs-lookup"><span data-stu-id="c8db5-124">Online courses and onsite courses are stored in separate tables, each of which has a foreign key that points to the `Course` table.</span></span> <span data-ttu-id="c8db5-125">コースの両方の種類に共通の情報は格納されているだけで、`Course`テーブル。</span><span class="sxs-lookup"><span data-stu-id="c8db5-125">Information common to both course types is stored only in the `Course` table.</span></span>

<span data-ttu-id="c8db5-126">[![image12](the-entity-framework-and-aspnet-getting-started-part-6/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-126">[![image12](the-entity-framework-and-aspnet-getting-started-part-6/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image3.png)</span></span>

<span data-ttu-id="c8db5-127">Entity Framework データ モデルを構成することができるように`OnlineCourse`と`OnsiteCourse`からエンティティを継承、`Course`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-127">You can configure the Entity Framework data model so that `OnlineCourse` and `OnsiteCourse` entities inherit from the `Course` entity.</span></span> <span data-ttu-id="c8db5-128">種類ごとに、すべての種類に共通のデータを格納するテーブルを参照する独立した各テーブルの個別のテーブルからエンティティ継承構造を生成するには、このパターンが呼び出された*型ごとにテーブル*(TPT) 継承します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-128">This pattern of generating an entity inheritance structure from separate tables for each type, with each separate table referring back to a table that stores data common to all types, is called *table per type* (TPT) inheritance.</span></span>

<span data-ttu-id="c8db5-129">一般に TPH 継承パターンでは TPT パターンが複雑な結合クエリのためにパフォーマンスを向上させる TPT 継承のパターンよりも、Entity Framework でに配信しています。</span><span class="sxs-lookup"><span data-stu-id="c8db5-129">TPH inheritance patterns generally deliver better performance in the Entity Framework than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span> <span data-ttu-id="c8db5-130">このチュートリアルでは、TPH 継承の実装方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-130">This walkthrough demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="c8db5-131">次の手順を実行することによってを実行してみましょう。</span><span class="sxs-lookup"><span data-stu-id="c8db5-131">You'll do that by performing the following steps:</span></span>

- <span data-ttu-id="c8db5-132">作成`Instructor`と`Student`から派生したエンティティ型`Person`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-132">Create `Instructor` and `Student` entity types that derive from `Person`.</span></span>
- <span data-ttu-id="c8db5-133">派生エンティティに関連するプロパティを移動、`Person`派生エンティティをエンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-133">Move properties that pertain to the derived entities from the `Person` entity to the derived entities.</span></span>
- <span data-ttu-id="c8db5-134">派生型では、プロパティに制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-134">Set constraints on properties in the derived types.</span></span>
- <span data-ttu-id="c8db5-135">ように、`Person`エンティティ抽象エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-135">Make the `Person` entity an abstract entity.</span></span>
- <span data-ttu-id="c8db5-136">各マップの派生エンティティを`Person`を決定する方法を指定する条件を持つテーブルかどうか、`Person`行の派生型を表します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-136">Map each derived entity to the `Person` table with a condition that specifies how to determine whether a `Person` row represents that derived type.</span></span>

## <a name="adding-instructor-and-student-entities"></a><span data-ttu-id="c8db5-137">インストラクターと学生エンティティの追加</span><span class="sxs-lookup"><span data-stu-id="c8db5-137">Adding Instructor and Student Entities</span></span>

<span data-ttu-id="c8db5-138">開く、 *SchoolModel.edmx*ファイルをデザイナーで使用されていない領域を右クリックして**追加**選択してから、**エンティティ***です。*</span><span class="sxs-lookup"><span data-stu-id="c8db5-138">Open the *SchoolModel.edmx* file, right-click an unoccupied area in the designer, select **Add**, then select **Entity***.*</span></span>

<span data-ttu-id="c8db5-139">[![image01](the-entity-framework-and-aspnet-getting-started-part-6/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-139">[![image01](the-entity-framework-and-aspnet-getting-started-part-6/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image5.png)</span></span>

<span data-ttu-id="c8db5-140">**エンティティの追加** ダイアログ ボックスに、名前、エンティティ`Instructor`設定とその**基本型**オプションを`Person`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-140">In the **Add Entity** dialog box, name the entity `Instructor` and set its **Base type** option to `Person`.</span></span>

<span data-ttu-id="c8db5-141">[![image02](the-entity-framework-and-aspnet-getting-started-part-6/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image7.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-141">[![image02](the-entity-framework-and-aspnet-getting-started-part-6/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image7.png)</span></span>

<span data-ttu-id="c8db5-142">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-142">Click **OK**.</span></span> <span data-ttu-id="c8db5-143">デザイナーを作成、`Instructor`から派生するエンティティ、`Person`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-143">The designer creates an `Instructor` entity that derives from the `Person` entity.</span></span> <span data-ttu-id="c8db5-144">新しいエンティティがないすべてのプロパティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-144">The new entity does not yet have any properties.</span></span>

<span data-ttu-id="c8db5-145">[![image03](the-entity-framework-and-aspnet-getting-started-part-6/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image9.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-145">[![image03](the-entity-framework-and-aspnet-getting-started-part-6/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image9.png)</span></span>

<span data-ttu-id="c8db5-146">作成する手順を繰り返して、`Student`からも派生エンティティ`Person`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-146">Repeat the procedure to create a `Student` entity that also derives from `Person`.</span></span>

<span data-ttu-id="c8db5-147">のみからそのプロパティに移動する必要があります、インストラクターがある雇用日、`Person`エンティティを`Instructor`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-147">Only instructors have hire dates, so you need to move that property from the `Person` entity to the `Instructor` entity.</span></span> <span data-ttu-id="c8db5-148">`Person` 、エンティティを右クリックし、`HireDate`プロパティをクリックして**切り取り**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-148">In the `Person` entity, right-click the `HireDate` property and click **Cut**.</span></span> <span data-ttu-id="c8db5-149">右クリックし、**プロパティ**で、`Instructor`エンティティとクリック**貼り付け**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-149">Then right-click **Properties** in the `Instructor` entity and click **Paste**.</span></span>

<span data-ttu-id="c8db5-150">[![image04](the-entity-framework-and-aspnet-getting-started-part-6/_static/image12.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image11.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-150">[![image04](the-entity-framework-and-aspnet-getting-started-part-6/_static/image12.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image11.png)</span></span>

<span data-ttu-id="c8db5-151">入社日、`Instructor`エンティティを null にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c8db5-151">The hire date of an `Instructor` entity cannot be null.</span></span> <span data-ttu-id="c8db5-152">右クリックし、`HireDate`プロパティ、をクリックして**プロパティ**、し、、**プロパティ**ウィンドウ変更`Nullable`に`False`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-152">Right-click the `HireDate` property, click **Properties**, and then in the **Properties** window change `Nullable` to `False`.</span></span>

<span data-ttu-id="c8db5-153">[![image05](the-entity-framework-and-aspnet-getting-started-part-6/_static/image14.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image13.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-153">[![image05](the-entity-framework-and-aspnet-getting-started-part-6/_static/image14.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image13.png)</span></span>

<span data-ttu-id="c8db5-154">移動する手順を繰り返して、`EnrollmentDate`プロパティから、`Person`エンティティを`Student`エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-154">Repeat the procedure to move the `EnrollmentDate` property from the `Person` entity to the `Student` entity.</span></span> <span data-ttu-id="c8db5-155">設定することを確認してください`Nullable`に`False`の`EnrollmentDate`プロパティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-155">Make sure that you also set `Nullable` to `False` for the `EnrollmentDate` property.</span></span>

<span data-ttu-id="c8db5-156">これで、`Person`エンティティに共通するプロパティのみ`Instructor`と`Student`エンティティ (ナビゲーション プロパティ、移動していない) とは別のエンティティのみ使用できます、継承構造の基本エンティティにします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-156">Now that a `Person` entity has only the properties that are common to `Instructor` and `Student` entities (aside from navigation properties, which you're not moving), the entity can only be used as a base entity in the inheritance structure.</span></span> <span data-ttu-id="c8db5-157">そのため、個別のエンティティとして扱われますしないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-157">Therefore, you need to ensure that it's never treated as an independent entity.</span></span> <span data-ttu-id="c8db5-158">右クリックし、`Person`エンティティで、**プロパティ**、し、**プロパティ**ウィンドウの値を変更する、**抽象**プロパティを**True**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-158">Right-click the `Person` entity, select **Properties**, and then in the **Properties** window change the value of the **Abstract** property to **True**.</span></span>

<span data-ttu-id="c8db5-159">[![image06](the-entity-framework-and-aspnet-getting-started-part-6/_static/image16.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image15.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-159">[![image06](the-entity-framework-and-aspnet-getting-started-part-6/_static/image16.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image15.png)</span></span>

## <a name="mapping-instructor-and-student-entities-to-the-person-table"></a><span data-ttu-id="c8db5-160">Person テーブルをインストラクターと学生エンティティのマッピング</span><span class="sxs-lookup"><span data-stu-id="c8db5-160">Mapping Instructor and Student Entities to the Person Table</span></span>

<span data-ttu-id="c8db5-161">Entity Framework を区別する方法を指示する必要があります`Instructor`と`Student`データベース内のエンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-161">Now you need to tell the Entity Framework how to differentiate between `Instructor` and `Student` entities in the database.</span></span>

<span data-ttu-id="c8db5-162">右クリックし、`Instructor`エンティティと選択**テーブル マッピング**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-162">Right-click the `Instructor` entity and select **Table Mapping**.</span></span> <span data-ttu-id="c8db5-163">**マッピングの詳細**ウィンドウで、をクリックして**テーブルまたはビューの追加**を選択し、**人**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-163">In the **Mapping Details** window, click **Add a Table or View** and select **Person**.</span></span>

<span data-ttu-id="c8db5-164">[![image07](the-entity-framework-and-aspnet-getting-started-part-6/_static/image18.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image17.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-164">[![image07](the-entity-framework-and-aspnet-getting-started-part-6/_static/image18.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image17.png)</span></span>

<span data-ttu-id="c8db5-165">をクリックして**条件を追加する**、し、 **HireDate**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-165">Click **Add a Condition**, and then select **HireDate**.</span></span>

<span data-ttu-id="c8db5-166">[![image09](the-entity-framework-and-aspnet-getting-started-part-6/_static/image20.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image19.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-166">[![image09](the-entity-framework-and-aspnet-getting-started-part-6/_static/image20.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image19.png)</span></span>

<span data-ttu-id="c8db5-167">変更**演算子**に**は**と**値/プロパティ**に**Not Null**です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-167">Change **Operator** to **Is** and **Value / Property** to **Not Null**.</span></span>

<span data-ttu-id="c8db5-168">[![image10](the-entity-framework-and-aspnet-getting-started-part-6/_static/image22.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image21.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-168">[![image10](the-entity-framework-and-aspnet-getting-started-part-6/_static/image22.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image21.png)</span></span>

<span data-ttu-id="c8db5-169">手順を繰り返して、`Students`にこのエンティティがマップされるを指定するエンティティ、`Person`時にテーブル、`EnrollmentDate`列は null ではありません。</span><span class="sxs-lookup"><span data-stu-id="c8db5-169">Repeat the procedure for the `Students` entity, specifying that this entity maps to the `Person` table when the `EnrollmentDate` column is not null.</span></span> <span data-ttu-id="c8db5-170">保存して、データ モデルを終了します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-170">Then save and close the data model.</span></span>

<span data-ttu-id="c8db5-171">クラスの新しいエンティティを作成し、デザイナーで使用できるようにするためにプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-171">Build the project in order to create the new entities as classes and make them available in the designer.</span></span>

## <a name="using-the-instructor-and-student-entities"></a><span data-ttu-id="c8db5-172">講師および学生エンティティを使用</span><span class="sxs-lookup"><span data-stu-id="c8db5-172">Using the Instructor and Student Entities</span></span>

<span data-ttu-id="c8db5-173">Student とインストラクター データでデータをバインドする作業に web ページを作成したときに、`Person`エンティティ セットとでフィルター処理、`HireDate`または`EnrollmentDate`受講者または講習においてインストラクターに返されるデータを制限するプロパティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-173">When you created the web pages that work with student and instructor data, you databound them to the `Person` entity set, and you filtered on the `HireDate` or `EnrollmentDate` property to restrict the returned data to students or instructors.</span></span> <span data-ttu-id="c8db5-174">ただし、ここでバインドすると各データ ソースの管理に、`Person`エンティティ セットにのみ指定できます`Student`または`Instructor`エンティティの種類を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-174">However, now when you bind each data source control to the `Person` entity set, you can specify that only `Student` or `Instructor` entity types should be selected.</span></span> <span data-ttu-id="c8db5-175">Entity Framework は、受講者および講習においてインストラクターに区別する方法を認識しているため、`Person`エンティティ セットを削除する、`Where`プロパティの設定を行うには手動で入力しました。</span><span class="sxs-lookup"><span data-stu-id="c8db5-175">Because the Entity Framework knows how to differentiate students and instructors in the `Person` entity set, you can remove the `Where` property settings you entered manually to do that.</span></span>

<span data-ttu-id="c8db5-176">Visual Studio デザイナーで、エンティティの種類を指定できます、`EntityDataSource`でコントロールを選択する必要があります、 **EntityTypeFilter**のドロップダウン ボックス、`Configure Data Source`ウィザードで、次の例で示すようにします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-176">In the Visual Studio Designer, you can specify the entity type that an `EntityDataSource` control should select in the **EntityTypeFilter** drop-down box of the `Configure Data Source` wizard, as shown in the following example.</span></span>

<span data-ttu-id="c8db5-177">[![image13](the-entity-framework-and-aspnet-getting-started-part-6/_static/image24.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image23.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-177">[![image13](the-entity-framework-and-aspnet-getting-started-part-6/_static/image24.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image23.png)</span></span>

<span data-ttu-id="c8db5-178">および、**プロパティ**を除去するウィンドウ`Where`不要になったために必要な次の例で示すように句の値。</span><span class="sxs-lookup"><span data-stu-id="c8db5-178">And in the **Properties** window you can remove `Where` clause values that are no longer needed, as shown in the following example.</span></span>

<span data-ttu-id="c8db5-179">[![image14](the-entity-framework-and-aspnet-getting-started-part-6/_static/image26.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image25.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-179">[![image14](the-entity-framework-and-aspnet-getting-started-part-6/_static/image26.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image25.png)</span></span>

<span data-ttu-id="c8db5-180">ただしのマークアップを変更したため`EntityDataSource`使用するコントロールを`ContextTypeName`実行することはできません、属性、**データ ソースの構成**でウィザード`EntityDataSource`を既に作成済みのコントロールです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-180">However, because you've changed the markup for `EntityDataSource` controls to use the `ContextTypeName` attribute, you cannot run the **Configure Data Source** wizard on `EntityDataSource` controls that you've already created.</span></span> <span data-ttu-id="c8db5-181">そのため、代わりにマークアップを変更することによって必要な変更を行うします。</span><span class="sxs-lookup"><span data-stu-id="c8db5-181">Therefore, you'll make the required changes by changing markup instead.</span></span>

<span data-ttu-id="c8db5-182">開く、 *Students.aspx*ページ。</span><span class="sxs-lookup"><span data-stu-id="c8db5-182">Open the *Students.aspx* page.</span></span> <span data-ttu-id="c8db5-183">`StudentsEntityDataSource`コントロールを削除、`Where`属性を追加、`EntityTypeFilter="Student"`属性。</span><span class="sxs-lookup"><span data-stu-id="c8db5-183">In the `StudentsEntityDataSource` control, remove the `Where` attribute and add an `EntityTypeFilter="Student"` attribute.</span></span> <span data-ttu-id="c8db5-184">マークアップは、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-184">The markup will now resemble the following example:</span></span>

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample1.aspx)]

<span data-ttu-id="c8db5-185">設定、`EntityTypeFilter`属性により、`EntityDataSource`コントロールは、指定されたエンティティ型だけを選択します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-185">Setting the `EntityTypeFilter` attribute ensures that the `EntityDataSource` control will select only the specified entity type.</span></span> <span data-ttu-id="c8db5-186">両方を取得する場合は`Student`と`Instructor`エンティティ型の場合は、この属性を設定しません。</span><span class="sxs-lookup"><span data-stu-id="c8db5-186">If you wanted to retrieve both `Student` and `Instructor` entity types, you would not set this attribute.</span></span> <span data-ttu-id="c8db5-187">(いずれかで複数のエンティティ型を取得するオプションがある`EntityDataSource`読み取り専用データ アクセスのため、コントロールを使用している場合にのみを制御します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-187">(You have the option of retrieving multiple entity types with one `EntityDataSource` control only if you're using the control for read-only data access.</span></span> <span data-ttu-id="c8db5-188">使用する場合、`EntityDataSource`挿入、更新、またはエンティティの削除を制御しにバインドされているエンティティ セットには、複数の種類を含めることができる場合、のみ、1 つのエンティティ型を使用することができ、この属性を設定する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="c8db5-188">If you're using an `EntityDataSource` control to insert, update, or delete entities, and if the entity set it's bound to can contain multiple types, you can only work with one entity type, and you have to set this attribute.)</span></span>

<span data-ttu-id="c8db5-189">手順を繰り返して、`SearchEntityDataSource`制御の一部のみを削除する点を除いて、`Where`属性を選択する`Student`プロパティを完全に削除せずにエンティティです。</span><span class="sxs-lookup"><span data-stu-id="c8db5-189">Repeat the procedure for the `SearchEntityDataSource` control, except remove only the part of the `Where` attribute that selects `Student` entities instead of removing the property altogether.</span></span> <span data-ttu-id="c8db5-190">コントロールの開始タグは、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-190">The opening tag of the control will now resemble the following example:</span></span>

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample2.aspx)]

<span data-ttu-id="c8db5-191">動作の確認、引き続き以前と同じようにページを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-191">Run the page to verify that it still works as it did before.</span></span>

<span data-ttu-id="c8db5-192">[![image15](the-entity-framework-and-aspnet-getting-started-part-6/_static/image28.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image27.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-192">[![image15](the-entity-framework-and-aspnet-getting-started-part-6/_static/image28.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image27.png)</span></span>

<span data-ttu-id="c8db5-193">新しいを使用するように、前のチュートリアルで作成した次のページを更新`Student`と`Instructor`の代わりにエンティティ`Person`エンティティ、それらを実行する前に、同様に動作することを確認します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-193">Update the following pages that you created in earlier tutorials so that they use the new `Student` and `Instructor` entities instead of `Person` entities, then run them to verify that they work as they did before:</span></span>

- <span data-ttu-id="c8db5-194">*StudentsAdd.aspx*、追加`EntityTypeFilter="Student"`を`StudentsEntityDataSource`コントロール。</span><span class="sxs-lookup"><span data-stu-id="c8db5-194">In *StudentsAdd.aspx*, add `EntityTypeFilter="Student"` to the `StudentsEntityDataSource` control.</span></span> <span data-ttu-id="c8db5-195">マークアップは、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-195">The markup will now resemble the following example:</span></span> 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample3.aspx)]

    <span data-ttu-id="c8db5-196">[![image16](the-entity-framework-and-aspnet-getting-started-part-6/_static/image30.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image29.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-196">[![image16](the-entity-framework-and-aspnet-getting-started-part-6/_static/image30.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image29.png)</span></span>
- <span data-ttu-id="c8db5-197">*About.aspx*、追加`EntityTypeFilter="Student"`を`StudentStatisticsEntityDataSource`を制御し、削除`Where="it.EnrollmentDate is not null"`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-197">In *About.aspx*, add `EntityTypeFilter="Student"` to the `StudentStatisticsEntityDataSource` control and remove `Where="it.EnrollmentDate is not null"`.</span></span> <span data-ttu-id="c8db5-198">マークアップは、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-198">The markup will now resemble the following example:</span></span> 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample4.aspx)]

    <span data-ttu-id="c8db5-199">[![image17](the-entity-framework-and-aspnet-getting-started-part-6/_static/image32.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image31.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-199">[![image17](the-entity-framework-and-aspnet-getting-started-part-6/_static/image32.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image31.png)</span></span>
- <span data-ttu-id="c8db5-200">*Instructors.aspx*と*InstructorsCourses.aspx*、追加`EntityTypeFilter="Instructor"`を`InstructorsEntityDataSource`を制御し、削除`Where="it.HireDate is not null"`です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-200">In *Instructors.aspx* and *InstructorsCourses.aspx*, add `EntityTypeFilter="Instructor"` to the `InstructorsEntityDataSource` control and remove `Where="it.HireDate is not null"`.</span></span> <span data-ttu-id="c8db5-201">内のマークアップ*Instructors.aspx*次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-201">The markup in *Instructors.aspx* now resembles the following example:</span></span> 

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample5.aspx)]

    <span data-ttu-id="c8db5-202">[![image18](the-entity-framework-and-aspnet-getting-started-part-6/_static/image34.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image33.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-202">[![image18](the-entity-framework-and-aspnet-getting-started-part-6/_static/image34.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image33.png)</span></span>

    <span data-ttu-id="c8db5-203">内のマークアップ*InstructorsCourses.aspx*は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-203">The markup in *InstructorsCourses.aspx* will now resemble the following example:</span></span>

    [!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-6/samples/sample6.aspx)]

    <span data-ttu-id="c8db5-204">[![image19](the-entity-framework-and-aspnet-getting-started-part-6/_static/image36.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image35.png)</span><span class="sxs-lookup"><span data-stu-id="c8db5-204">[![image19](the-entity-framework-and-aspnet-getting-started-part-6/_static/image36.png)](the-entity-framework-and-aspnet-getting-started-part-6/_static/image35.png)</span></span>

<span data-ttu-id="c8db5-205">これらの変更の結果として、いくつかの方法で、Contoso 大学アプリケーションの保守容易性を改善しました。</span><span class="sxs-lookup"><span data-stu-id="c8db5-205">As a result of these changes, you've improved the Contoso University application's maintainability in several ways.</span></span> <span data-ttu-id="c8db5-206">UI レイヤーからの選択と検証ロジックを移動した (*.aspx*マークアップ) と、データ アクセス レイヤーの不可欠な部分です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-206">You've moved selection and validation logic out of the UI layer (*.aspx* markup) and made it an integral part of the data access layer.</span></span> <span data-ttu-id="c8db5-207">これにより、アプリケーション コードに加えた変更する可能性があります将来的に、データベース スキーマやデータ モデルから分離します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-207">This helps to isolate your application code from changes that you might make in the future to the database schema or the data model.</span></span> <span data-ttu-id="c8db5-208">たとえば、受講者が教師の補助として採用可能性があり、入社日が得られるためことを決定する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c8db5-208">For example, you could decide that students might be hired as teachers' aids and therefore would get a hire date.</span></span> <span data-ttu-id="c8db5-209">インストラクターから受講者を区別し、データ モデルを更新する新しいプロパティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c8db5-209">You could then add a new property to differentiate students from instructors and update the data model.</span></span> <span data-ttu-id="c8db5-210">受講者、雇用された日付を表示したい以外を変更する必要は web アプリケーションのコードではありません。</span><span class="sxs-lookup"><span data-stu-id="c8db5-210">No code in the web application would need to change except where you wanted to show a hire date for students.</span></span> <span data-ttu-id="c8db5-211">追加の利点の 1 つ`Instructor`と`Student`エンティティは、コードがより容易にするときに参照されているよりも理解できるものである`Person`実際に受講者をしたオブジェクトを教師です。</span><span class="sxs-lookup"><span data-stu-id="c8db5-211">Another benefit of adding `Instructor` and `Student` entities is that your code is more readily understandable than when it referred to `Person` objects that were actually students or instructors.</span></span>

<span data-ttu-id="c8db5-212">Entity Framework での継承パターンを実装する方法を見てきましたようになりました。</span><span class="sxs-lookup"><span data-stu-id="c8db5-212">You've now seen one way to implement an inheritance pattern in the Entity Framework.</span></span> <span data-ttu-id="c8db5-213">次のチュートリアルでは、Entity Framework がデータベースにアクセスするより詳細に制御するためにストアド プロシージャを使用する方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="c8db5-213">In the following tutorial, you'll learn how to use stored procedures in order to have more control over how the Entity Framework accesses the database.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="c8db5-214">[前へ](the-entity-framework-and-aspnet-getting-started-part-5.md)
[次へ](the-entity-framework-and-aspnet-getting-started-part-7.md)</span><span class="sxs-lookup"><span data-stu-id="c8db5-214">[Previous](the-entity-framework-and-aspnet-getting-started-part-5.md)
[Next](the-entity-framework-and-aspnet-getting-started-part-7.md)</span></span>