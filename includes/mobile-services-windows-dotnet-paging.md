

1. Visual Studio で、「**モバイル サービスでのデータの使用**」チュートリアルを実行したときに変更したプロジェクトを開きます。
2. **F5** キーを押してアプリケーションを実行し、**[Insert a TodoItem]** ボックスにテキストを入力して、**[Save]** をクリックします。
3. 前の手順を少なくとも 3 回繰り返して、TodoItem テーブルに項目を 3 つ以上保存します。
4. MainPage.xaml.cs ファイルで、**RefreshTodoItems** メソッドを次のコードに置き換えます。
   
        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }
   
      このクエリは、データ バインド中に実行されると、完了マークが付けられていない上位 3 つの項目を返します。
5. **F5** キーを押してアプリケーションを実行します。
   
      TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。
6. (省略可能) ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示します。
   
       クエリの URI では、`Take(3)` メソッドがクエリ オプション `$top=3` に変換されていることに注目してください。
7. 再度 **RefreshTodoItems** メソッドを次のコードに置き換えます。
   
        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }
   
       このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。
   
   > [!NOTE]
   > このチュートリアルでは、ハードコーディングされたページング値を <strong>Take</strong> メソッドと <strong>Skip</strong> メソッドに渡すことで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。また、<strong>IncludeTotalCount</strong> メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できる項目の合計数を取得することもできます。
   > 
   > 
8. (省略可能) 再度、モバイル サービスに送信された要求の URI を表示します。
   
       クエリの URI では、`Skip(3)` メソッドがクエリ オプション `$skip=3` に変換されていることに注目してください。

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=Oct15_HO3-->