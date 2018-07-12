---
title: ML.NET を使用してニューヨークのタクシー運賃を予測する (回帰)
description: 回帰のシナリオで ML.NET を使用する方法について説明します。
author: aditidugar
ms.author: johalex
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9706dad0a8e32651496e0404be4501c2c70e9d75
ms.sourcegitcommit: ed7b4b9b77d35e94a35a2634e8c874f46603fb2b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36948632"
---
# <a name="tutorial-use-mlnet-to-predict-new-york-taxi-fares-regression"></a><span data-ttu-id="ec7d2-103">チュートリアル: ML.NET を使用してニューヨークのタクシー運賃を予測する (回帰)</span><span class="sxs-lookup"><span data-stu-id="ec7d2-103">Tutorial: Use ML.NET to predict New York taxi fares (regression)</span></span>

> [!NOTE]
> <span data-ttu-id="ec7d2-104">このトピックでは現在プレビュー段階である ML.NET を参照しており、資料が変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-104">This topic refers to ML.NET, which is currently in Preview, and material may be subject to change.</span></span> <span data-ttu-id="ec7d2-105">詳しくは、[ML.NET の概要](https://www.microsoft.com/net/learn/apps/machine-learning-and-ai/ml-dotnet)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-105">For more information, visit [the ML.NET introduction](https://www.microsoft.com/net/learn/apps/machine-learning-and-ai/ml-dotnet).</span></span>

<span data-ttu-id="ec7d2-106">このチュートリアルでは、ML.NET を使用して、ニューヨーク市のタクシー運賃を予測する[回帰モデル](../resources/glossary.md#regression)を構築する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-106">This tutorial illustrates how to use ML.NET to build a [regression model](../resources/glossary.md#regression) for predicting New York City taxi fares.</span></span>

<span data-ttu-id="ec7d2-107">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-107">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="ec7d2-108">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-108">Understand the problem</span></span>
> * <span data-ttu-id="ec7d2-109">適切な機械学習タスクを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-109">Select the appropriate machine learning task</span></span>
> * <span data-ttu-id="ec7d2-110">データを準備して理解する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-110">Prepare and understand the data</span></span>
> * <span data-ttu-id="ec7d2-111">学習パイプラインを作成する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-111">Create a learning pipeline</span></span>
> * <span data-ttu-id="ec7d2-112">データを読み込んで変換する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-112">Load and transform the data</span></span>
> * <span data-ttu-id="ec7d2-113">学習アルゴリズムを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-113">Choose a learning algorithm</span></span>
> * <span data-ttu-id="ec7d2-114">モデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="ec7d2-114">Train the model</span></span>
> * <span data-ttu-id="ec7d2-115">モデルを評価する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-115">Evaluate the model</span></span>
> * <span data-ttu-id="ec7d2-116">モデルを使用して予測を行う</span><span class="sxs-lookup"><span data-stu-id="ec7d2-116">Use the model for predictions</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec7d2-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec7d2-117">Prerequisites</span></span>

* <span data-ttu-id="ec7d2-118">[Visual Studio 2017 15.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)が ".NET Core クロスプラット フォーム開発" とともにインストールされていること。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-118">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the ".NET Core cross-platform development" workload installed.</span></span>

## <a name="understand-the-problem"></a><span data-ttu-id="ec7d2-119">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-119">Understand the problem</span></span>

<span data-ttu-id="ec7d2-120">この問題の中心となるのは、**ニューヨーク市のタクシー旅行の運賃の予測**です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-120">This problem is centered around **predicting the fare of a taxi trip in New York City**.</span></span> <span data-ttu-id="ec7d2-121">一見すると、単に乗車距離に依存すると思われるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-121">At first glance, it may seem to depend simply on the distance traveled.</span></span> <span data-ttu-id="ec7d2-122">しかし、ニューヨークのタクシー会社は追加の乗客数や現金でなくクレジット カードによる支払いなど、その他の要因によって請求額を変えます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-122">However, taxi vendors in New York charge varying amounts for other factors such as additional passengers or paying with a credit card instead of cash.</span></span>

## <a name="select-the-appropriate-machine-learning-task"></a><span data-ttu-id="ec7d2-123">適切な機械学習タスクを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-123">Select the appropriate machine learning task</span></span>

<span data-ttu-id="ec7d2-124">タクシー運賃を予測するには、最初に適切な機械学習タスクを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-124">To predict the taxi fare, you first select the appropriate machine learning task.</span></span> <span data-ttu-id="ec7d2-125">ここでは、データ セット内のその他の要因に基づいて実際の値 (価格を表す倍精度値) を予測しようとしています。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-125">You are looking to predict a real value (a double that represents price) based on the other factors in the dataset.</span></span> <span data-ttu-id="ec7d2-126">そのため、[**回帰**](../resources/glossary.md#regression)タスクを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-126">You choose a [**regression**](../resources/glossary.md#regression) task.</span></span>

## <a name="create-a-console-application"></a><span data-ttu-id="ec7d2-127">コンソール アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-127">Create a console application</span></span>

1. <span data-ttu-id="ec7d2-128">Visual Studio 2017 を開きます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-128">Open Visual Studio 2017.</span></span> <span data-ttu-id="ec7d2-129">**[ファイル]** > **[新規作成]** > **[プロジェクト]** をメニュー バーから選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-129">Select **File** > **New** > **Project** from the menu bar.</span></span> <span data-ttu-id="ec7d2-130">**[新しいプロジェクト]** ダイアログで、**[Visual C#]** ノードを選択し、**[.NET Core]** ノードを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-130">In the **New Project** dialog, select the **Visual C#** node followed by the **.NET Core** node.</span></span> <span data-ttu-id="ec7d2-131">次に、**[コンソール アプリ (.NET Core)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-131">Then select the **Console App (.NET Core)** project template.</span></span> <span data-ttu-id="ec7d2-132">**[名前]** テキスト ボックスに「TaxiFarePrediction」と入力し、**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-132">In the **Name** text box, type "TaxiFarePrediction" and then select the **OK** button.</span></span>

2. <span data-ttu-id="ec7d2-133">プロジェクトに *Data* という名前のディレクトリを作成して、データ セット ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-133">Create a directory named *Data* in your project to save the data set files:</span></span>

    <span data-ttu-id="ec7d2-134">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-134">In **Solution Explorer**, right-click the project and select **Add** > **New Folder**.</span></span> <span data-ttu-id="ec7d2-135">「Data」と入力して Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-135">Type "Data" and hit Enter.</span></span>

3. <span data-ttu-id="ec7d2-136">**Microsoft.ML NuGet パッケージ**をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-136">Install the **Microsoft.ML NuGet Package**:</span></span>

    <span data-ttu-id="ec7d2-137">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-137">In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="ec7d2-138">[パッケージ ソース] として "nuget.org" を選択し、**[参照]** タブを選択して「**Microsoft.ML**」を検索します。一覧からそのパッケージを選択し、**[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-138">Choose "nuget.org" as the Package source, select the **Browse** tab, search for **Microsoft.ML**, select that package in the list, and select the **Install** button.</span></span> <span data-ttu-id="ec7d2-139">**[変更のプレビュー]** ダイアログの **[OK]** を選択します。表示されているパッケージのライセンス条項に同意する場合は、**[ライセンスの同意]** ダイアログの **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-139">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span>

## <a name="prepare-and-understand-the-data"></a><span data-ttu-id="ec7d2-140">データを準備して理解する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-140">Prepare and understand the data</span></span>

1. <span data-ttu-id="ec7d2-141">[taxi-fare-train.csv](https://github.com/dotnet/machinelearning/blob/master/test/data/taxi-fare-train.csv) データ セットと [taxi-fare-test.csv](https://github.com/dotnet/machinelearning/blob/master/test/data/taxi-fare-test.csv) データ セットをダウンロードして、前の手順で作成済みの *Data* フォルダーに保存します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-141">Download the [taxi-fare-train.csv](https://github.com/dotnet/machinelearning/blob/master/test/data/taxi-fare-train.csv) and the [taxi-fare-test.csv](https://github.com/dotnet/machinelearning/blob/master/test/data/taxi-fare-test.csv) data sets and save them to the *Data* folder you've created at the previous step.</span></span> <span data-ttu-id="ec7d2-142">これらのデータ セットを使用して、機械学習モデルをトレーニングし、モデルの正確度を評価します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-142">We use these data sets to train the machine learning model and then evaluate how accurate the model is.</span></span> <span data-ttu-id="ec7d2-143">これらのデータ セットは、[NYC TLC Taxi Trip データ セット](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml)から取得したものです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-143">These data sets are originally from the [NYC TLC Taxi Trip data set](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml).</span></span>

2. <span data-ttu-id="ec7d2-144">**ソリューション エクスプローラー**で、各 \*.csv ファイルを右クリックし、**[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-144">In **Solution Explorer**, right-click each of the \*.csv files and select **Properties**.</span></span> <span data-ttu-id="ec7d2-145">**[詳細設定]** で、**[出力ディレクトリにコピー]** の値を **[常時]** に変更します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-145">Under **Advanced**, change the value of **Copy to Output Directory** to **Always**.</span></span>

3. <span data-ttu-id="ec7d2-146">**taxi-fare-train.csv** データ セットを開き、最初の行の列ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-146">Open the **taxi-fare-train.csv** data set and look at column headers in the first row.</span></span> <span data-ttu-id="ec7d2-147">各列を確認してください。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-147">Take a look at each of the columns.</span></span> <span data-ttu-id="ec7d2-148">データについて把握し、どの列が**特徴**で、どの列が**ラベル**であるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-148">Understand the data and decide which columns are **features** and which one is the **label**.</span></span>

<span data-ttu-id="ec7d2-149">**ラベル**は、予測する列の識別子です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-149">The **label** is the identifier of the column you want to predict.</span></span> <span data-ttu-id="ec7d2-150">識別された**特徴**は、ラベルを予測するために使用します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-150">The identified **features** are used to predict the label.</span></span>

<span data-ttu-id="ec7d2-151">提供されているデータ セットには、次の列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-151">The provided data set contains the following columns:</span></span>

* <span data-ttu-id="ec7d2-152">**vendor_id:** タクシー会社の ID は特徴です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-152">**vendor_id:** The ID of the taxi vendor is a feature.</span></span>
* <span data-ttu-id="ec7d2-153">**rate_code:** タクシー旅行のレートの種類は特徴です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-153">**rate_code:** The rate type of the taxi trip is a feature.</span></span>
* <span data-ttu-id="ec7d2-154">**passenger_count:** 旅行の乗客数は特徴です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-154">**passenger_count:** The number of passengers on the trip is a feature.</span></span>
* <span data-ttu-id="ec7d2-155">**trip_time_in_secs:** 旅行の所要時間です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-155">**trip_time_in_secs:** The amount of time the trip took.</span></span> <span data-ttu-id="ec7d2-156">旅行が終わる前に、旅行の運賃を予測したいと考えます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-156">You want to predict the fare of the trip before the trip is completed.</span></span> <span data-ttu-id="ec7d2-157">その時点では、旅行の所要時間はわかりません。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-157">At that moment you don't know how long the trip would take.</span></span> <span data-ttu-id="ec7d2-158">したがって、旅行の所要時間は特徴ではなく、この列はモデルから除外します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-158">Thus, the trip time is not a feature and you'll exclude this column from the model.</span></span>
* <span data-ttu-id="ec7d2-159">**trip_distance:** 旅行距離は特徴です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-159">**trip_distance:** The distance of the trip is a feature.</span></span>
* <span data-ttu-id="ec7d2-160">**payment_type:** 支払い方法 (現金またはクレジット カード) は特徴です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-160">**payment_type:** The payment method (cash or credit card) is a feature.</span></span>
* <span data-ttu-id="ec7d2-161">**fare_amount:** 支払った合計タクシー運賃はラベルです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-161">**fare_amount:** The total taxi fare paid is the label.</span></span>

## <a name="create-data-classes"></a><span data-ttu-id="ec7d2-162">データ クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-162">Create data classes</span></span>

<span data-ttu-id="ec7d2-163">入力データと予測のためのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-163">Create classes for the input data and the predictions:</span></span>

1. <span data-ttu-id="ec7d2-164">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-164">In **Solution Explorer**, right-click the project, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="ec7d2-165">**[新しい項目の追加]** ダイアログ ボックスで、**[クラス]** を選択し、**[名前]** フィールドを *TaxiTrip.cs* に変更します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-165">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *TaxiTrip.cs*.</span></span> <span data-ttu-id="ec7d2-166">次に、**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-166">Then, select the **Add** button.</span></span>
1. <span data-ttu-id="ec7d2-167">以下の `using` ディレクティブを新しいファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-167">Add the following `using` directives to the new file:</span></span>

   [!code-csharp[AddUsings](../../../samples/machine-learning/tutorials/TaxiFarePrediction/TaxiTrip.cs#1 "Add necessary usings")]

<span data-ttu-id="ec7d2-168">既存のクラス定義を削除し、2 つのクラス `TaxiTrip` と `TaxiTripFarePrediction` を含む次のコードを *TaxiTrip.cs* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-168">Remove the existing class definition and add the following code, which has two classes `TaxiTrip` and `TaxiTripFarePrediction`, to the *TaxiTrip.cs* file:</span></span>

[!code-csharp[DefineTaxiTrip](../../../samples/machine-learning/tutorials/TaxiFarePrediction/TaxiTrip.cs#2 "Define the taxi trip and fare predictions classes")]

<span data-ttu-id="ec7d2-169">`TaxiTrip` は入力データ クラスであり、各データ セット列の定義が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-169">`TaxiTrip` is the input data class and has definitions for each of the data set columns.</span></span> <span data-ttu-id="ec7d2-170">[Column](xref:Microsoft.ML.Runtime.Api.ColumnAttribute) 属性を使用して、データ セット内のソース列のインデックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-170">Use the [Column](xref:Microsoft.ML.Runtime.Api.ColumnAttribute) attribute to specify the indices of the source columns in the data set.</span></span>

<span data-ttu-id="ec7d2-171">`TaxiTripFarePrediction` クラスは、予測結果を表すために使用します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-171">The `TaxiTripFarePrediction` class is used to represent predicted results.</span></span> <span data-ttu-id="ec7d2-172">このクラスには、`Score` [ColumnName](xref:Microsoft.ML.Runtime.Api.ColumnNameAttribute) 属性が適用された 1 つの浮動小数点型 (`FareAmount`) フィールドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-172">It has a single float (`FareAmount`) field with a `Score` [ColumnName](xref:Microsoft.ML.Runtime.Api.ColumnNameAttribute) attribute applied.</span></span> <span data-ttu-id="ec7d2-173">**Score** 列は ML.NET 内で特別な列です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-173">The **Score** column is the special column in ML.NET.</span></span> <span data-ttu-id="ec7d2-174">モデルは、この列に予測値を出力します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-174">The model outputs predicted values into that column.</span></span>

## <a name="define-data-and-model-paths"></a><span data-ttu-id="ec7d2-175">データおよびモデルのパスを定義する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-175">Define data and model paths</span></span>

<span data-ttu-id="ec7d2-176">*Program.cs* ファイルに戻り、データ セットを含むファイルと、モデルを保存するためのファイルへのパスを保持するための 3 つのフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-176">Go back to the *Program.cs* file and add three fields to hold the paths to the files with data sets and the file to save the model:</span></span>

* <span data-ttu-id="ec7d2-177">`_datapath` には、モデルのトレーニングに使用するデータ セットがあるファイルへのパスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-177">`_datapath` contains the path to the file with the data set used to train the model.</span></span>
* <span data-ttu-id="ec7d2-178">`_testdatapath` には、モデルの評価に使用するデータ セットがあるファイルへのパスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-178">`_testdatapath` contains the path to the file with the data set used to evaluate the model.</span></span>
* <span data-ttu-id="ec7d2-179">`_modelpath` には、トレーニング済みモデルが格納されるファイルへのパスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-179">`_modelpath` contains the path to the file where the trained model is stored.</span></span>

<span data-ttu-id="ec7d2-180">`Main` メソッドのすぐ上に次のコードを追加して、それらのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-180">Add the following code right above the `Main` method to specify those paths:</span></span>

[!code-csharp[InitializePaths](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#2 "Define variables to store the data file paths")]

<span data-ttu-id="ec7d2-181">上記のコードをコンパイルするには、*Program.cs* ファイルの先頭に次の `using` ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-181">To make the preceding code compile, add the following `using` directives at the top of the *Program.cs* file:</span></span>

[!code-csharp[AddUsingsForPaths](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#17 "Using statements for path definitions")]

## <a name="create-a-learning-pipeline"></a><span data-ttu-id="ec7d2-182">学習パイプラインを作成する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-182">Create a learning pipeline</span></span>

<span data-ttu-id="ec7d2-183">以下の追加の `using` ディレクティブを、*Program.cs* ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-183">Add the following additional `using` directives to the top of the *Program.cs* file:</span></span>

[!code-csharp[AddUsings](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#1 "Add necessary usings")]

<span data-ttu-id="ec7d2-184">`Main` で、`Console.WriteLine("Hello World!")` を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-184">In `Main`, replace the `Console.WriteLine("Hello World!")` with the following code:</span></span>

```csharp
PredictionModel<TaxiTrip, TaxiTripFarePrediction> model = Train();
```

<span data-ttu-id="ec7d2-185">`Train` メソッドは、モデルをトレーニングします。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-185">The `Train` method trains the model.</span></span> <span data-ttu-id="ec7d2-186">次のコードを使用して、`Main` の直下にそのメソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-186">Create that method just below `Main`, using the following code:</span></span>

```csharp
public static PredictionModel<TaxiTrip, TaxiTripFarePrediction> Train()
{

}
```

<span data-ttu-id="ec7d2-187">学習パイプラインは、モデルのトレーニングに必要なすべてのデータとアルゴリズムを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-187">The learning pipeline loads all of the data and algorithms necessary to train the model.</span></span> <span data-ttu-id="ec7d2-188">`Train` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-188">Add the following code into the `Train` method:</span></span>

```csharp
var pipeline = new LearningPipeline();
```

## <a name="load-and-transform-data"></a><span data-ttu-id="ec7d2-189">データを読み込んで変換する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-189">Load and transform data</span></span>

<span data-ttu-id="ec7d2-190">学習パイプラインが実行する最初の手順は、トレーニング データ セットからデータを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-190">The first step that the learning pipeline performs is loading data from the training data set.</span></span> <span data-ttu-id="ec7d2-191">ここでは、トレーニング データ セットは、`_datapath` フィールドによってパスが定義されるテキスト ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-191">In our case, training data set is stored in the text file with a path defined by the `_datapath` field.</span></span> <span data-ttu-id="ec7d2-192">そのファイルには列名を含んだヘッダーがあるので、データの読み込み中に最初の行は無視されます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-192">That file contains the header with the column names, so the first row should be ignored while loading data.</span></span> <span data-ttu-id="ec7d2-193">ファイル内の列はコンマ (",") で区切られます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-193">Columns in the file are separated by the comma (",").</span></span> <span data-ttu-id="ec7d2-194">`Train` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-194">Add the following code into the `Train` method:</span></span>

```csharp
pipeline.Add(new TextLoader(_datapath).CreateFrom<TaxiTrip>(useHeader: true, separator: ','));
```

<span data-ttu-id="ec7d2-195">次の手順では、`TaxiTrip` クラス内で定義された名前によって列を参照します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-195">In the next steps we refer to the columns by the names defined in the `TaxiTrip` class.</span></span>

<span data-ttu-id="ec7d2-196">モデルのトレーニングおよび評価が行われる場合、**Label** 列内の値が予測される適切な値と見なされます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-196">When the model is trained and evaluated, the values in the **Label** column are considered as correct values to be predicted.</span></span> <span data-ttu-id="ec7d2-197">タクシー旅行の運賃を予測したいので、`FareAmount` 列を **Label** 列にコピーします。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-197">As we want to predict the taxi trip fare, copy the `FareAmount` column into the **Label** column.</span></span> <span data-ttu-id="ec7d2-198">そのためには、<xref:Microsoft.ML.Transforms.ColumnCopier> を使用して、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-198">To do that, use <xref:Microsoft.ML.Transforms.ColumnCopier> and add the following code:</span></span>

```csharp
pipeline.Add(new ColumnCopier(("FareAmount", "Label")));
```

<span data-ttu-id="ec7d2-199">モデルをトレーニングするアルゴリズムには、**数値**の特徴が必要であるため、カテゴリ データ (`VendorId`、`RateCode`、および `PaymentType`) 値を数字に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-199">The algorithm that trains the model requires **numeric** features, so you have to transform the categorical data (`VendorId`, `RateCode`, and `PaymentType`) values into numbers.</span></span> <span data-ttu-id="ec7d2-200">そのためには、<xref:Microsoft.ML.Transforms.CategoricalOneHotVectorizer> (さまざまな数値キーの値をそれぞれ、各列内のさまざまな値に割り当てます) を使用し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-200">To do that, use <xref:Microsoft.ML.Transforms.CategoricalOneHotVectorizer>, which assigns different numeric key values to the different values in each of the columns, and add the following code:</span></span>

```csharp
pipeline.Add(new CategoricalOneHotVectorizer("VendorId",
                                             "RateCode",
                                             "PaymentType"));
```

<span data-ttu-id="ec7d2-201">データの準備の最後の手順では、<xref:Microsoft.ML.Transforms.ColumnConcatenator> 変換クラスを使用して、すべての特徴列を **Features** 列に結合します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-201">The last step in data preparation combines all of the feature columns into the **Features** column using the <xref:Microsoft.ML.Transforms.ColumnConcatenator> transformation class.</span></span> <span data-ttu-id="ec7d2-202">この手順は、ラーナーが **Features** 列からの特徴のみを処理する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-202">This step is necessary as a learner processes only features from the **Features** column.</span></span> <span data-ttu-id="ec7d2-203">次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-203">Add the following code:</span></span>

```csharp
pipeline.Add(new ColumnConcatenator("Features",
                                    "VendorId",
                                    "RateCode",
                                    "PassengerCount",
                                    "TripDistance",
                                    "PaymentType"));
```

<span data-ttu-id="ec7d2-204">`TripTime` 列 (データ セット ファイル内の `trip_time_in_secs` 列に対応する) が含まれていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-204">Notice that the `TripTime` column, which corresponds to the `trip_time_in_secs` column in the data set file, isn't included.</span></span> <span data-ttu-id="ec7d2-205">既に確認したとおり、この列は有効な予測特徴ではありません。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-205">You already determined that it isn't a useful prediction feature.</span></span>

> [!NOTE]
> <span data-ttu-id="ec7d2-206">これらの手順を成功させるには、前述の指定した順序でパイプラインに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-206">These steps must be added to the pipeline in the order specified above for successful execution.</span></span>

## <a name="choose-a-learning-algorithm"></a><span data-ttu-id="ec7d2-207">学習アルゴリズムを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-207">Choose a learning algorithm</span></span>

<span data-ttu-id="ec7d2-208">データをパイプラインに追加して正しい入力形式に変換したら、学習アルゴリズム (**ラーナー**) を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-208">After adding the data to the pipeline and transforming it into the correct input format, you select a learning algorithm (**learner**).</span></span> <span data-ttu-id="ec7d2-209">ラーナーはモデルをトレーニングします。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-209">The learner trains the model.</span></span> <span data-ttu-id="ec7d2-210">この問題に対しては**回帰タスク**を選択するので、<xref:Microsoft.ML.Trainers.FastTreeRegressor> ラーナー (ML.NET によって提供される回帰ラーナーの 1 つ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-210">You chose a **regression task** for this problem, so you add a <xref:Microsoft.ML.Trainers.FastTreeRegressor> learner, which is one of the regression learners provided by ML.NET.</span></span>

<span data-ttu-id="ec7d2-211"><xref:Microsoft.ML.Trainers.FastTreeRegressor> ラーナーは、勾配ブースティングを利用します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-211"><xref:Microsoft.ML.Trainers.FastTreeRegressor> learner utilizes gradient boosting.</span></span> <span data-ttu-id="ec7d2-212">勾配ブースティングは、回帰問題に対応する機械学習の手法です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-212">Gradient boosting is a machine learning technique for regression problems.</span></span> <span data-ttu-id="ec7d2-213">この手法では、それぞれの回帰ツリーを段階的に構築します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-213">It builds each regression tree in a step-wise fashion.</span></span> <span data-ttu-id="ec7d2-214">また、定義済みの損失関数を使用して、各ステップの誤差を測定し、次の段階で修正します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-214">It uses a pre-defined loss function to measure the error in each step and correct for it in the next.</span></span> <span data-ttu-id="ec7d2-215">結果は予測モデルですが、実際は弱い予測モデルの集合です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-215">The result is a prediction model that is actually an ensemble of weaker prediction models.</span></span> <span data-ttu-id="ec7d2-216">勾配ブースティングについて詳しくは、「[Boosted Decision Tree Regression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression)」(ブーストされたデシジョン ツリー回帰) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-216">For more information about gradient boosting, see [Boosted Decision Tree Regression](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression).</span></span>

<span data-ttu-id="ec7d2-217">前の手順で追加したデータ処理コードの後にある `Train` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-217">Add the following code into the `Train` method following the data processing code added in the previous step:</span></span>

```csharp
pipeline.Add(new FastTreeRegressor());
```

<span data-ttu-id="ec7d2-218">前述のすべての手順は個々のステートメントとしてパイプラインに追加しましたが、C# には使いやすいコレクションの初期化構文があるため、パイプラインの作成と初期化が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-218">You added all the preceding steps to the pipeline as individual statements, but C# has a handy collection initialization syntax that makes it simpler to create and initialize the pipeline.</span></span> <span data-ttu-id="ec7d2-219">次のコードを使用して、これまでに追加したコードを `Train` メソッドに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-219">Replace the code you added so far to the `Train` method with the following code:</span></span>

[!code-csharp[CreatePipeline](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#3 "Create and initialize the learning pipeline")]

## <a name="train-the-model"></a><span data-ttu-id="ec7d2-220">モデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="ec7d2-220">Train the model</span></span>

<span data-ttu-id="ec7d2-221">最後の手順は、モデルのトレーニングです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-221">The final step is to train the model.</span></span> <span data-ttu-id="ec7d2-222">ここまで、パイプライン内の項目は何も実行されていません。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-222">Until this point, nothing in the pipeline has been executed.</span></span> <span data-ttu-id="ec7d2-223">`pipeline.Train<TInput, TOutput>` メソッドは、`TInput` 型のインスタンスを取り込んで `TOutput` 型のインスタンスを出力するモデルを生成します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-223">The `pipeline.Train<TInput, TOutput>` method produces the model that takes in an instance of the `TInput` type and outputs an instance of the `TOutput` type.</span></span> <span data-ttu-id="ec7d2-224">`Train` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-224">Add the following code into the `Train` method:</span></span>

[!code-csharp[TrainMOdel](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#4 "Train your model")]

<span data-ttu-id="ec7d2-225">以上です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-225">And that's it!</span></span> <span data-ttu-id="ec7d2-226">これで機械学習モデルのトレーニングが終了し、ニューヨーク市のタクシー運賃を予測できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-226">You have successfully trained a machine learning model that can predict taxi fares in NYC.</span></span> <span data-ttu-id="ec7d2-227">それでは、モデルの正確度について理解してから、モデルを使用してタクシー運賃の値を予測する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-227">Now let's take a look to understand how accurate the model is and learn how to use it to predict taxi fare values.</span></span>

### <a name="save-the-model"></a><span data-ttu-id="ec7d2-228">モデルを保存する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-228">Save the model</span></span>

<span data-ttu-id="ec7d2-229">次の手順に進む前に、`Train` メソッドの末尾に次のコードを追加して、モデルを .zip ファイルに保存します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-229">Before you go onto the next step, save the model to a .zip file by adding the following code at the end of the `Train` method:</span></span>

[!code-csharp[SaveModel](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#5 "Save the model asynchronously and return the model")]

<span data-ttu-id="ec7d2-230">`model.WriteAsync` の呼び出しに `await` ステートメントを追加した場合は、`Train` メソッドをタスクを返す非同期メソッドに変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-230">Adding the `await` statement to the `model.WriteAsync` call means that the `Train` method must be changed to an async method that returns a task.</span></span> <span data-ttu-id="ec7d2-231">次のコードに示すように、`Train` のシグネチャを変更します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-231">Modify the signature of `Train` as shown in the following code:</span></span>

[!code-csharp[AsyncTraining](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#6 "Make the Train method async and return a task.")]

<span data-ttu-id="ec7d2-232">`Train` メソッドの戻り値の型を変更した場合は、次のコードに示すように、`Main` メソッドで `Train` を呼び出すコードに `await` を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-232">Changing the return type of the `Train` method means you have to add an `await` to the code that calls `Train` in the `Main` method as shown in the following code:</span></span>

[!code-csharp[AwaitTraining](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#7 "Await the Train method")]

<span data-ttu-id="ec7d2-233">`Main` メソッドで `await` を使用する場合は、`Main` メソッドに `async` 修飾子が必要です。このメソッドは `Task` を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-233">Using `await` in the `Main` method means the `Main` method must have the `async` modifier and return a `Task`:</span></span>

[!code-csharp[AsyncMain](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#8 "Make the Main method async and return a task.")]

<span data-ttu-id="ec7d2-234">また、次の `using` ディレクティブをファイルの先頭に追加する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-234">You also need to add the following `using` directive at the top of the file:</span></span>

[!code-csharp[UsingTasks](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#9 "Add System.Threading.Tasks. to your usings.")]

<span data-ttu-id="ec7d2-235">`async Main` メソッドは C# 7.1 に追加された機能であり、プロジェクトの既定の言語バージョンは C# 7.0 であるため、言語バージョンを C# 7.1 以降に変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-235">Because the `async Main` method is the feature added in C# 7.1 and the default language version of the project is C# 7.0, you need to change the language version to C# 7.1 or higher.</span></span> <span data-ttu-id="ec7d2-236">そのためには、**ソリューション エクスプローラー**でプロジェクト ノードを右クリックして、**[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-236">To do that, right-click the project node in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="ec7d2-237">**[ビルド]** タブを選択し、**[詳細設定]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-237">Select the **Build** tab and select the **Advanced** button.</span></span> <span data-ttu-id="ec7d2-238">ドロップダウンで **[C# 7.1]** (またはそれ以降のバージョン) を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-238">In the dropdown, select  **C# 7.1** (or a higher version).</span></span> <span data-ttu-id="ec7d2-239">**[OK]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-239">Select the **OK** button.</span></span>

## <a name="evaluate-the-model"></a><span data-ttu-id="ec7d2-240">モデルを評価する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-240">Evaluate the model</span></span>

<span data-ttu-id="ec7d2-241">評価は、モデルがラベル値を適切に予測するかどうかを確認するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-241">Evaluation is the process of checking how well the model predicts label values.</span></span> <span data-ttu-id="ec7d2-242">モデルのトレーニング時に使用しなかったデータを適切に予測できるかどうかが重要になります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-242">It's important that the model makes good predictions on data that was not used to train the model.</span></span> <span data-ttu-id="ec7d2-243">そのための方法の 1 つとしては、このチュートリアルで行ったように、データをトレーニング用のデータ セットとテスト用のデータ セットに分けます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-243">One way to do this is to split the data into train and test data sets, as it's done in this tutorial.</span></span> <span data-ttu-id="ec7d2-244">トレーニング用データでのモデルのトレーニングは完了しているので、テスト用データでモデルが適切に機能するかどうかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-244">Now that you've trained the model on the train data, you can see how well it performs on the test data.</span></span>

<span data-ttu-id="ec7d2-245">`Main` メソッドに戻り、`Train` メソッドの呼び出しの下に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-245">Go back to the `Main` method and add the following code beneath the call to the `Train`method:</span></span>

[!code-csharp[Evaluate](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#10 "Evaluate the model.")]

<span data-ttu-id="ec7d2-246">`Evaluate` メソッドはモデルを評価します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-246">The `Evaluate` method evaluates the model.</span></span> <span data-ttu-id="ec7d2-247">そのメソッドを作成するには、`Train` メソッドの下に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-247">To create that method, add the following code below the `Train` method:</span></span>

```csharp
private static void Evaluate(PredictionModel<TaxiTrip, TaxiTripFarePrediction> model)
{

}
```

<span data-ttu-id="ec7d2-248">次のコードを `Evaluate` メソッドに追加して、テスト データの読み込みを設定します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-248">Add the following code into the `Evaluate` method to setup loading of the test data:</span></span>

[!code-csharp[LoadTestData](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#12 "Load the test data.")]

<span data-ttu-id="ec7d2-249">次のコードを追加してモデルを評価し、評価メトリックを生成します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-249">Add the following code to evaluate the model and produce the evaluation metrics:</span></span>

[!code-csharp[EvaluateAndMeasure](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#13 "Evaluate the model and its predictions.")]

<span data-ttu-id="ec7d2-250">[RMS](../resources/glossary.md##root-of-mean-squared-error-rmse) は回帰モデルの評価メトリックの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-250">[RMS](../resources/glossary.md##root-of-mean-squared-error-rmse) is one of the evaluation metrics of the regression model.</span></span> <span data-ttu-id="ec7d2-251">RMS が低いほど、優れたモデルになります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-251">The lower it is, the better the model is.</span></span> <span data-ttu-id="ec7d2-252">`Evaluate` メソッドに次のコードを追加することで、RMS 値を表示します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-252">Add the following code into the `Evaluate` method to display the RMS value:</span></span>

[!code-csharp[DisplayRMS](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#14 "Display the RMS metric.")]

<span data-ttu-id="ec7d2-253">[RSquared](../resources/glossary.md#coefficient-of-determination) は回帰モデルのもう 1 つの評価メトリックです。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-253">[RSquared](../resources/glossary.md#coefficient-of-determination) is another evaluation metric of the regression models.</span></span> <span data-ttu-id="ec7d2-254">RSquared は 0 から 1 までの値を取ります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-254">RSquared takes values between 0 and 1.</span></span> <span data-ttu-id="ec7d2-255">値が 1 に近づくほど、優れたモデルになります。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-255">The closer its value is to 1, the better the model is.</span></span> <span data-ttu-id="ec7d2-256">次のコードを `Evaluate` メソッドに追加して、RSquared 値を表示します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-256">Add the following code into the `Evaluate` method to display the RSquared value:</span></span>

[!code-csharp[DisplayRSquared](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#15 "Display the RSquared metric.")]

## <a name="use-the-model-for-predictions"></a><span data-ttu-id="ec7d2-257">モデルを使用して予測を行う</span><span class="sxs-lookup"><span data-stu-id="ec7d2-257">Use the model for predictions</span></span>

<span data-ttu-id="ec7d2-258">次に、モデルが正しく機能しているかどうかを確認するためのテスト シナリオを格納するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-258">Next, create a class to house test scenarios that you can use to make sure the model is working correctly:</span></span>

1. <span data-ttu-id="ec7d2-259">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-259">In **Solution Explorer**, right-click the project, and then select **Add** > **New Item**.</span></span>
1. <span data-ttu-id="ec7d2-260">**[新しい項目の追加]** ダイアログ ボックスで、**[クラス]** を選択し、**[名前]** フィールドを *TestTrips.cs* に変更します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-260">In the **Add New Item** dialog box, select **Class** and change the **Name** field to *TestTrips.cs*.</span></span> <span data-ttu-id="ec7d2-261">次に、**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-261">Then, select the **Add** button.</span></span>
1. <span data-ttu-id="ec7d2-262">次の例に示すように、静的になるようにクラスを変更します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-262">Modify the class to be static like in the following example:</span></span>

   [!code-csharp[StaticClass](../../../samples/machine-learning/tutorials/TaxiFarePrediction/TestTrips.cs#1 "Change class to be a static class.")]

<span data-ttu-id="ec7d2-263">このチュートリアルでは、このクラス内の 1 つのテスト用の旅行を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-263">This tutorial uses one test trip within this class.</span></span> <span data-ttu-id="ec7d2-264">モデルを試行するために後で他のシナリオを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-264">Later you can add other scenarios to experiment with the model.</span></span> <span data-ttu-id="ec7d2-265">`TestTrips` クラスに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-265">Add the following code into the `TestTrips` class:</span></span>

[!code-csharp[TestData](../../../samples/machine-learning/tutorials/TaxiFarePrediction/TestTrips.cs#2 "Create aq trip to predict its cost.")]

<span data-ttu-id="ec7d2-266">この旅行の実際の運賃は 29.5 です。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-266">This trip's actual fare is 29.5.</span></span> <span data-ttu-id="ec7d2-267">このモデルでは運賃が予測されるので、プレース ホルダーとして 0 を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-267">Use 0 as a placeholder, as the model will predict the fare.</span></span>

<span data-ttu-id="ec7d2-268">指定した旅行の運賃を予測するには、*Program.cs* ファイルに戻り、次のコードを `Main` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-268">To predict the fare of the specified trip, go back to the *Program.cs* file and add the following code into the `Main` method:</span></span>

[!code-csharp[Predict](../../../samples/machine-learning/tutorials/TaxiFarePrediction/Program.cs#16 "Try a prediction.")]

<span data-ttu-id="ec7d2-269">プログラムを実行し、テスト ケースに対して予測されたタクシー運賃を確認します。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-269">Run the program to see the predicted taxi fare for your test case.</span></span>

<span data-ttu-id="ec7d2-270">おめでとうございます! </span><span class="sxs-lookup"><span data-stu-id="ec7d2-270">Congratulations!</span></span> <span data-ttu-id="ec7d2-271">これで、タクシー旅行運賃を予測する機械学習モデルが正常に構築され、その正確度が評価され、このモデルを使用した予測が行われました。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-271">You've now successfully built a machine learning model for predicting taxi trip fares, evaluated its accuracy, and used it to make predictions.</span></span> <span data-ttu-id="ec7d2-272">このチュートリアルのソース コードは [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/TaxiFarePrediction) リポジトリで確認できます。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-272">You can find the source code for this tutorial at the [dotnet/samples](https://github.com/dotnet/samples/tree/master/machine-learning/tutorials/TaxiFarePrediction) repository.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec7d2-273">次の手順</span><span class="sxs-lookup"><span data-stu-id="ec7d2-273">Next steps</span></span>

<span data-ttu-id="ec7d2-274">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-274">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="ec7d2-275">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-275">Understand the problem</span></span>
> * <span data-ttu-id="ec7d2-276">適切な機械学習タスクを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-276">Select the appropriate machine learning task</span></span>
> * <span data-ttu-id="ec7d2-277">データを準備して理解する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-277">Prepare and understand the data</span></span>
> * <span data-ttu-id="ec7d2-278">学習パイプラインを作成する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-278">Create a learning pipeline</span></span>
> * <span data-ttu-id="ec7d2-279">データを読み込んで変換する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-279">Load and transform the data</span></span>
> * <span data-ttu-id="ec7d2-280">学習アルゴリズムを選択する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-280">Choose a learning algorithm</span></span>
> * <span data-ttu-id="ec7d2-281">モデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="ec7d2-281">Train the model</span></span>
> * <span data-ttu-id="ec7d2-282">モデルを評価する</span><span class="sxs-lookup"><span data-stu-id="ec7d2-282">Evaluate the model</span></span>
> * <span data-ttu-id="ec7d2-283">モデルを使用して予測を行う</span><span class="sxs-lookup"><span data-stu-id="ec7d2-283">Use the model for predictions</span></span>

<span data-ttu-id="ec7d2-284">学習を続けてその他のサンプルを確認するには、GitHub リポジトリをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ec7d2-284">Check out our GitHub repository to continue learning and find more samples.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="ec7d2-285">dotnet/machinelearning GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="ec7d2-285">dotnet/machinelearning GitHub repository</span></span>](https://github.com/dotnet/machinelearning/)