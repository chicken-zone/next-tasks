## MongoDBのタスクモデルを作成
- モデルとは、MongoDBのコレクションに対して操作を行うためのインターフェース
- モデルを利用することでMongoDBのデータ操作を簡単に行えるようになる
- srcディレクトリにmodelsディレクトリを作成しその中にtask.tsを作成
    - タスクに必要な属性を定義する
    - Taskのtitle、説明(description)、期限(dueDate)、タスクの完了状態(isCompleted)を定義
    ```
    export interface Task {
        title:string;
        description:string;
        dueDate:string;
        isCompleted:boolean;
    }
    ```
    - MongoDBではデータ登録時に自動的にIDが付与される為、IDの定義は不要
    - mongooseからDocumentをimportしこのDocumentクラスはMongoDBのデータが一般的に持つプロパティを含んだクラスでIDもこのDocumentクラスの中に定義されている

    - TaskのプロパティとDocumentのプロパティを持つインターフェースを定義する
    ```
    export interface TaskDocument extends Task,Document{
       createdAt: Date;
        updatedAt:Date;
    }
    ```

    - TaskデータのSchemaを定義
    - Schema(スキーマ)とはMongoDBのデータ構造を定義するものでフィールド名、データ型、制約、デフォルト値などを設定できる
    - typeは先頭が大文字となる
    - required:trueは必須項目となり、必須項目ではない場合は、省略する
    - Schemaの第二引数にオブジェクトでtimestamps:trueを設定するとcreatedAtとupdatedAtが自動で追加される
    ```
    const taskSchema = new mongoose.Schema<TaskDocument>({
    title:{
        type:String,
        required:true,
    },
    description:{
        type:String,
    },
    dueDate:{
        type: String,
        required: true,
    },
    isCompleted:{
        type: Boolean,
        default:false,
    }
    },{timestamps:true});
    ```
    - taskSchemaを使って、モデルを作成する
    - すでにタスクモデルが作成されている場合は下記で取得出きる
    ```
    export const TaskModel = mongoose.models.Task 
    ```
    - モデルの中にタスクが存在しない場合は、SchemaオブジェクトのtaskSchemaを渡して新しくモデルを作成する
    ```
    || mongoose.model("Task",taskSchema)
    ```