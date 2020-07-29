```python
import win32com.client as win32
outlook=win32.Dispatch("Outlook.Application")
mail=outlook.CreateItem(0)
mail.Recipients.Add(收件地址)
mail.Subject=题目
mail.BodyFormat=2 #html format
mail.HTMLBody=""
mail.Send()
```

