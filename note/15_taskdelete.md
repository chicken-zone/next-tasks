## タスクの削除機能を実装
- actionsディレクトリのtask.ts内にdeleteTask関数を作成する
    - タスクの削除はTaskModelのdeleteOneメソッドを使用して第一引数の条件で指定したIDのタスクを削除する
    - 第二引数の更新内容は不要となる
    - 下記でServerActionsは完成
    ```
    export const deleteTask = async (id: string, state: FormState) => {
      try {
        await connectDb();
        await TaskModel.deleteOne({ _id: id });
      } catch (error) {
        state.error = 'タスクの削除に失敗しました';
        return state;
      }
      redirect('/');
    };
    ```
    - TaskDeleteButton.tsxの修正を行う
    - useFormstate等を利用する為、先頭に"use client"を追加
    - deleteTask関数にtaskのidを渡す為、下記を記述
    ```
    const deleteTaskWithId = deleteTask.bind(null, id);
    ```
    - ServerActionsの初期状態を定義
    ```
      const initialState: FormState = { error: '' };
    ```
    - formActionをformタグのaction属性に渡す
    ```
      const [state, formAction] = useFormState(deleteTaskWithId, initialState);
    
      return (
        <form action={formAction}>
            <SubmitButton />
        </form>
      );
    ```
    - またタスク削除ボタンにはエラーメッセージを表示するスペースがない為、useEffectでエラーがある場合にはアラートで通知する
    ```
      useEffect(() => {
        if (state && state.error !== '') {
          alert(state.error);
        }
      }, [state]);
    ```
    - SubmitButtonコンポーネントを作成
    ```
      const SubmitButton = () => {
        const { pending } = useFormStatus();

        return (
          <button
            type="submit"
            disabled={pending}
            className="hover:text-gray-700 
          text-lg cursor-pointer disabled:bg-gray-400"
          >
            <FaTrashAlt />
          </button>
        );  
      };
    ```
    - ブラウザで削除ボタンをクリックするとタスクが削除されることが確認でき、MongoDBのデータにも存在しないことがわかる為、削除機能を実装されていることが確認できる