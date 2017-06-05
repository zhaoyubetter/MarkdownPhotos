## AS 插件开发
* 获取用户选择到的内容（如：layout）  

	 ```
	 @Override
    public void actionPerformed(AnActionEvent e) {  
		final Editor editor = e.getData(PlatformDataKeys.EDITOR);
        final SelectionModel selectionModel = editor.getSelectionModel();
        final String selectedText = selectionModel.getSelectedText();
        if(StringUtil.isNotEmpty(selectedText)) {
            System.out.println(selectedText);
        }    
    }