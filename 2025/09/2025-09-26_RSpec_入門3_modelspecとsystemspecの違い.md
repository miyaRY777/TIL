
---

# ✅ Model Spec と System Spec の違い

## 1. **Model Spec**

- **役割**  
    アプリの **ビジネスロジックやデータのルール（バリデーションやメソッド）** を確認するテスト。  
    Rails でいうと **`app/models` 配下のコード** をテストします。
    
- **特徴**
    
    - データベースに関連する処理を中心にチェック
    - **小さい単位のテスト**（ユニットテスト）
    - 外部とのやりとりは基本しない
        
- **具体例**
    
    ```ruby
    RSpec.describe Task, type: :model do
      describe 'バリデーション' do
        it 'タイトルがなければ無効' do
          task = Task.new(title: nil)
          expect(task).not_to be_valid
        end
      end
    
      describe '#done?' do
        it 'status が done の場合 true を返す' do
          task = Task.new(status: :done)
          expect(task.done?).to eq true
        end
      end
    end
    ```
    
    → データの正しさやモデルのメソッドの挙動をテスト。
    

---

## 2. **System Spec**

- **役割**  
    実際に **ユーザーがブラウザで操作する流れ** を確認するテスト。  
    Rails でいうと、画面遷移やフォーム入力を含めて、**アプリ全体の挙動をE2Eテスト**します。
    
- **特徴**
    
    - ブラウザ（Capybara + Selenium）を操作
    - **大きい単位のテスト**（結合テスト / E2Eテスト）
    - 画面の表示内容やクリック・入力の動作を確認
        
- **具体例**
    
    ```ruby
    RSpec.describe 'タスク管理', type: :system do
      it 'ユーザーがタスクを作成できること' do
        visit new_task_path
        fill_in 'タイトル', with: 'RSpecの勉強'
        fill_in '内容', with: 'model spec と system spec の違い'
        click_button '登録'
        
        expect(page).to have_content 'タスクが作成されました'
        expect(page).to have_content 'RSpecの勉強'
      end
    end
    ```
    
    → ユーザーが「タスクを作る」シナリオを、実際の画面操作でテスト。
    

---

## 3. **違いをまとめると**

|項目|Model Spec|System Spec|
|---|---|---|
|テスト対象|**モデルのロジックやバリデーション**|**画面操作の流れ（ユーザー操作）**|
|テストの粒度|小さい（単体テスト）|大きい（結合テスト/E2E）|
|使う機能|ActiveRecord, モデルのメソッド|Capybara, Selenium, ブラウザ|
|実行速度|速い（軽量）|遅い（ブラウザ起動が必要）|
|例|「title が必須」|「フォームに入力して登録できる」|

---

💡 イメージ

- **Model Spec**：プログラムの部品（エンジンやネジ）を単品チェック
    
- **System Spec**：車を実際に走らせて「ちゃんと動くか」確認
    

---

