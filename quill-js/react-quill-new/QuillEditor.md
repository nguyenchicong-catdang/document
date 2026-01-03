# components/QuillEditor.jsx
```jsx
import { useState } from "react"
import ReactQuill from "react-quill-new"
import 'react-quill-new/dist/quill.snow.css'

export default function QuillEditor() {
   const [editorHtml, setEditorHtml] = useState(''); // Dùng để hiển thị
   const [jsonValue, setJsonValue] = useState(''); // Dùng để gửi form
   const onChangeHandler = (content, delta, source, editor) => {
      setEditorHtml(content); // Cập nhật HTML cho editor
      const rowJson = JSON.stringify(editor.getContents()); // Lấy định dạng Delta (JSON)
      setJsonValue(rowJson)
   }
   return (
      <div style={{margin: '10px'}}>
         {/* Input này sẽ gửi dữ liệu JSON lên Server qua Form */}
         <input type="hidden" name="content" value={jsonValue} />
         <ReactQuill
            theme="snow"
            value={editorHtml}
            onChange={onChangeHandler}
         />
      </div>
   )
}

```
