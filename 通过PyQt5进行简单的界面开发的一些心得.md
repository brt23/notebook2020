# 通过PyQt5进行简单的界面绘制的一些心得

## 界面设计

PyQt5界面设计的方式有两种，分别是直接用代码描述和使用Qt Designer绘制。

非常推荐使用使用Qt Designer绘制界面，因为该软件可以提供可视化界面，通过可视化界面交互直观的进行界面设计。

Qt Designer绘制完成后会得到.ui后缀的界面设计文件，通过pyuic5工具将ui文件编译成python文件。命令如下：

```
pyuic5 -o myUI.py myUI.ui
```

## 多线程动作

如果某一项界面操作会执行非常长的时间，在单线程的模式下，主界面程序会进入未响应的状态。因此需要将这种耗时长的动作转变成子线程，通过多线程的模式进行设计。

## 实例

需要设计一套用于操作pytorch进行模型训练的界面工具。

该界面设计了一个用于展示训练过程的**控制台**，以及对应模型训练的三个操作步骤：**训练数据路径选择**、**模型保存位置选择**、**训练**。还在菜单栏设计了一个弹出窗口，弹出窗口可以进行**超参数的修改**。

### 主界面设计

主界面是通过Qt Designer绘制，以下是编译后的python文件。

```python
# -*- coding: utf-8 -*-

# Form implementation generated from reading ui file '.\console.ui'
#
# Created by: PyQt5 UI code generator 5.9.2
#
# WARNING! All changes made in this file will be lost!

from PyQt5 import QtCore, QtGui, QtWidgets

class Ui_MainWindow(object):
    def setupUi(self, MainWindow):
        MainWindow.setObjectName("MainWindow")
        MainWindow.resize(800, 634)
        self.centralwidget = QtWidgets.QWidget(MainWindow)
        self.centralwidget.setObjectName("centralwidget")
        self.textBrowser = QtWidgets.QTextBrowser(self.centralwidget)
        self.textBrowser.setGeometry(QtCore.QRect(10, 10, 781, 341))
        self.textBrowser.setAutoFillBackground(False)
        self.textBrowser.setObjectName("textBrowser")
        self.label = QtWidgets.QLabel(self.centralwidget)
        self.label.setGeometry(QtCore.QRect(20, 370, 201, 16))
        self.label.setObjectName("label")
        self.label_2 = QtWidgets.QLabel(self.centralwidget)
        self.label_2.setGeometry(QtCore.QRect(20, 430, 161, 16))
        self.label_2.setObjectName("label_2")
        self.horizontalLayoutWidget = QtWidgets.QWidget(self.centralwidget)
        self.horizontalLayoutWidget.setGeometry(QtCore.QRect(20, 450, 761, 31))
        self.horizontalLayoutWidget.setObjectName("horizontalLayoutWidget")
        self.horizontalLayout = QtWidgets.QHBoxLayout(self.horizontalLayoutWidget)
        self.horizontalLayout.setContentsMargins(0, 0, 0, 0)
        self.horizontalLayout.setObjectName("horizontalLayout")
        self.lineEdit_2 = QtWidgets.QLineEdit(self.horizontalLayoutWidget)
        self.lineEdit_2.setEnabled(False)
        self.lineEdit_2.setObjectName("lineEdit_2")
        self.horizontalLayout.addWidget(self.lineEdit_2)
        self.pushButton_2 = QtWidgets.QPushButton(self.horizontalLayoutWidget)
        self.pushButton_2.setObjectName("pushButton_2")
        self.horizontalLayout.addWidget(self.pushButton_2)
        self.horizontalLayoutWidget_2 = QtWidgets.QWidget(self.centralwidget)
        self.horizontalLayoutWidget_2.setGeometry(QtCore.QRect(20, 390, 761, 31))
        self.horizontalLayoutWidget_2.setObjectName("horizontalLayoutWidget_2")
        self.horizontalLayout_2 = QtWidgets.QHBoxLayout(self.horizontalLayoutWidget_2)
        self.horizontalLayout_2.setContentsMargins(0, 0, 0, 0)
        self.horizontalLayout_2.setObjectName("horizontalLayout_2")
        self.lineEdit = QtWidgets.QLineEdit(self.horizontalLayoutWidget_2)
        self.lineEdit.setEnabled(False)
        self.lineEdit.setText("")
        self.lineEdit.setDragEnabled(False)
        self.lineEdit.setReadOnly(False)
        self.lineEdit.setObjectName("lineEdit")
        self.horizontalLayout_2.addWidget(self.lineEdit)
        self.pushButton = QtWidgets.QPushButton(self.horizontalLayoutWidget_2)
        self.pushButton.setObjectName("pushButton")
        self.horizontalLayout_2.addWidget(self.pushButton)
        self.label_3 = QtWidgets.QLabel(self.centralwidget)
        self.label_3.setGeometry(QtCore.QRect(20, 490, 641, 16))
        self.label_3.setObjectName("label_3")
        self.pushButton_3 = QtWidgets.QPushButton(self.centralwidget)
        self.pushButton_3.setGeometry(QtCore.QRect(20, 530, 761, 51))
        self.pushButton_3.setObjectName("pushButton_3")
        self.label_4 = QtWidgets.QLabel(self.centralwidget)
        self.label_4.setGeometry(QtCore.QRect(60, 510, 521, 16))
        self.label_4.setObjectName("label_4")
        MainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QtWidgets.QMenuBar(MainWindow)
        self.menubar.setGeometry(QtCore.QRect(0, 0, 800, 23))
        self.menubar.setObjectName("menubar")
        self.menu = QtWidgets.QMenu(self.menubar)
        self.menu.setObjectName("menu")
        MainWindow.setMenuBar(self.menubar)
        self.statusbar = QtWidgets.QStatusBar(MainWindow)
        self.statusbar.setObjectName("statusbar")
        MainWindow.setStatusBar(self.statusbar)
        self.actionEpochs = QtWidgets.QAction(MainWindow)
        self.actionEpochs.setObjectName("actionEpochs")
        self.menu.addAction(self.actionEpochs)
        self.menubar.addAction(self.menu.menuAction())

        self.retranslateUi(MainWindow)
        QtCore.QMetaObject.connectSlotsByName(MainWindow)

    def retranslateUi(self, MainWindow):
        _translate = QtCore.QCoreApplication.translate
        MainWindow.setWindowTitle(_translate("MainWindow", "模型训练工具"))
        MainWindow.setWindowIcon(QtGui.QIcon("favicon.ico"))
        self.label.setText(_translate("MainWindow", "第一步：选择训练数据所在的文件夹"))
        self.label_2.setText(_translate("MainWindow", "第二步：选择模型保存位置"))
        self.lineEdit_2.setPlaceholderText(_translate("MainWindow", "选择模型文件保存路径"))
        self.pushButton_2.setText(_translate("MainWindow", "选择模型保存路径"))
        self.lineEdit.setPlaceholderText(_translate("MainWindow", "选择训练数据所在文件夹路径"))
        self.pushButton.setText(_translate("MainWindow", "选择数据读取路径"))
        self.label_3.setText(_translate("MainWindow", "第三步：开始训练 训练完成后将会输出一个模型文件到模型保存位置"))
        self.pushButton_3.setText(_translate("MainWindow", "训练"))
        self.label_4.setText(_translate("MainWindow", "（注：任何提前终止训练的情况都将不会得到模型文件）"))
        self.menu.setTitle(_translate("MainWindow", "超参数配置"))
        self.actionEpochs.setText(_translate("MainWindow", "Set Param"))

```

### 弹出窗口设计

弹出窗口是通过Qt Designer绘制，以下是编译后的python文件。

```python
# -*- coding: utf-8 -*-

# Form implementation generated from reading ui file '.\yoy.ui'
#
# Created by: PyQt5 UI code generator 5.9.2
#
# WARNING! All changes made in this file will be lost!

from PyQt5 import QtCore, QtGui, QtWidgets

class Ui_Dialog(object):
    def setupUi(self, Dialog):
        Dialog.setObjectName("Dialog")
        Dialog.resize(283, 222)
        self.label = QtWidgets.QLabel(Dialog)
        self.label.setGeometry(QtCore.QRect(13, 10, 81, 20))
        self.label.setObjectName("label")
        self.label_2 = QtWidgets.QLabel(Dialog)
        self.label_2.setGeometry(QtCore.QRect(10, 80, 91, 20))
        self.label_2.setObjectName("label_2")
        self.label_3 = QtWidgets.QLabel(Dialog)
        self.label_3.setGeometry(QtCore.QRect(10, 150, 111, 20))
        self.label_3.setObjectName("label_3")
        self.widget = QtWidgets.QWidget(Dialog)
        self.widget.setGeometry(QtCore.QRect(10, 170, 261, 41))
        self.widget.setObjectName("widget")
        self.horizontalLayout = QtWidgets.QHBoxLayout(self.widget)
        self.horizontalLayout.setContentsMargins(0, 0, 0, 0)
        self.horizontalLayout.setObjectName("horizontalLayout")
        self.lineEdit = QtWidgets.QLineEdit(self.widget)
        self.lineEdit.setEnabled(True)
        self.lineEdit.setObjectName("lineEdit")
        self.horizontalLayout.addWidget(self.lineEdit)
        self.pushButton = QtWidgets.QPushButton(self.widget)
        self.pushButton.setObjectName("pushButton")
        self.horizontalLayout.addWidget(self.pushButton)
        self.widget1 = QtWidgets.QWidget(Dialog)
        self.widget1.setGeometry(QtCore.QRect(10, 100, 261, 41))
        self.widget1.setObjectName("widget1")
        self.horizontalLayout_3 = QtWidgets.QHBoxLayout(self.widget1)
        self.horizontalLayout_3.setContentsMargins(0, 0, 0, 0)
        self.horizontalLayout_3.setObjectName("horizontalLayout_3")
        self.lineEdit_3 = QtWidgets.QLineEdit(self.widget1)
        self.lineEdit_3.setEnabled(True)
        self.lineEdit_3.setObjectName("lineEdit_3")
        self.horizontalLayout_3.addWidget(self.lineEdit_3)
        self.pushButton_3 = QtWidgets.QPushButton(self.widget1)
        self.pushButton_3.setObjectName("pushButton_3")
        self.horizontalLayout_3.addWidget(self.pushButton_3)
        self.widget2 = QtWidgets.QWidget(Dialog)
        self.widget2.setGeometry(QtCore.QRect(10, 30, 261, 41))
        self.widget2.setObjectName("widget2")
        self.horizontalLayout_2 = QtWidgets.QHBoxLayout(self.widget2)
        self.horizontalLayout_2.setContentsMargins(0, 0, 0, 0)
        self.horizontalLayout_2.setObjectName("horizontalLayout_2")
        self.lineEdit_2 = QtWidgets.QLineEdit(self.widget2)
        self.lineEdit_2.setEnabled(True)
        self.lineEdit_2.setObjectName("lineEdit_2")
        self.horizontalLayout_2.addWidget(self.lineEdit_2)
        self.pushButton_2 = QtWidgets.QPushButton(self.widget2)
        self.pushButton_2.setObjectName("pushButton_2")
        self.horizontalLayout_2.addWidget(self.pushButton_2)

        self.retranslateUi(Dialog)
        QtCore.QMetaObject.connectSlotsByName(Dialog)

    def retranslateUi(self, Dialog):
        _translate = QtCore.QCoreApplication.translate
        Dialog.setWindowTitle(_translate("Dialog", "Dialog"))
        Dialog.setWindowIcon(QtGui.QIcon("favicon.ico"))
        self.label.setText(_translate("Dialog", "设置 Epochs"))
        self.label_2.setText(_translate("Dialog", "设置 Batch Size"))
        self.label_3.setText(_translate("Dialog", "设置 Learning Rate"))
        self.pushButton.setText(_translate("Dialog", "设置"))
        self.pushButton_3.setText(_translate("Dialog", "设置"))
        self.pushButton_2.setText(_translate("Dialog", "设置"))
```

### 主程序设计

设计一个名为：TrainModelUi的类，该类中会实例化所有的功能。该类是继承 PyQt5.QtWidgets.QMainWindow 类，拥有QMainWindow的所有方法。

```python
from PyQt5.QtWidgets import QMainWindow

class TrainModelUi(QMainWindow):
    def __init__(self):
        super().__init__()
```



#### 绘制主界面

将绘制好的主界面Ui_MainWindow类实例化，通过setupUi方法并传入TrainModelUi对象（也就是QMainWindow对象）进行界面绑定。最后通过TrainModelUi对象（也就是QMainWindow对象）的show方法将画好的界面绘制在屏幕上。

```python
from PyQt5.QtWidgets import QApplication, QMainWindow

class TrainModelUi(QMainWindow):

    def __init__(self):
        super().__init__()
        self.main_window = Ui_MainWindow()
        self.main_window.setupUi(self) 
        self.show()
        
if __name__ == '__main__':
    app = QApplication(sys.argv)
    console = TrainModelUi()
    sys.exit(app.exec_())
```

#### 主界面功能绑定

主界面中设计了三个按钮，下面依次赋予按钮实际功能。

##### 选择训练数据目录按钮

Ui_MainWindow.pushButton被设计为用于选择训练数据目录的功能，下面来实现这个功能:

```python
class DataDirControl:

    def __init__(self, window):
        # window被设计为传入界面对象，通过该对象可以直接操作界面
        self.window = window
        # 这里运用了QT中信号和槽的模式
        # pushButton.clicked的信号绑定了self.chooseDataDir函数
        # 在pushButton点击后，会执行self.chooseDataDir
        self.window.main_window.pushButton.clicked.connect(self.chooseDataDir)


    def chooseDataDir(self):
        # 通过PyQt5.QtWidgets.QFileDialog中getExistingDirectory的功能
        # 设计一个弹出后选择文件夹的对话框
        directory = QFileDialog.getExistingDirectory(self.window, '训练数据文件夹路路径', '/')
        # 当获取的路径不为空时，将选择的路径输入lineEdit中
        # 通过lineEdit文本框显示
        if len(directory) != 0:
            self.window.main_window.lineEdit.setText(directory)
```

##### 选择模型保存路径按钮

Ui_MainWindow.pushButton_2被设计为用于选择模型保存路径的功能，下面来实现这个功能：

```python
# 该功能和Ui_MainWindow.pushButton的实现方式完全一样
class ModelDirControl:

    def __init__(self, window):
        self.window = window
        self.window.main_window.pushButton_2.clicked.connect(self.chooseModelDir)


    def chooseModelDir(self):
        directory = QFileDialog.getExistingDirectory(self.window, '模型保存路径', '/')
        if len(directory) != 0:
            self.window.main_window.lineEdit_2.setText(directory)
```

##### 启动训练按钮

Ui_MainWindow.pushButton_3被设计用于启动pytorch进行模型训练的功能功能，以下为该功能的实现：

```python
class TrainControl:

    def __init__(self, window, hyper_param):
        self.window = window
        self.window.main_window.pushButton_3.clicked.connect(self.trianModel)
        self.hyper_param = hyper_param
        self.abort_flag = False
        self.train_thread = None


    def trianModel(self):
        # 获取前两步设定的路径
        train_datapath = self.window.main_window.lineEdit.text()
        model_savepath = self.window.main_window.lineEdit_2.text()
		
        # 对路径进行检查，如果路径不符合格式要求或者不存在则弹出对应的警告框
        if len(train_datapath) == 0 or not os.path.isdir(train_datapath):
            # PyQt中QMessageBox是一个易用的消息弹窗，在窗口弹出时，主界面会被冻结
            # 有多种类型的弹窗，可以查询Qt的Doc。这里使用的是critical弹窗，一般用于表示严重警告
            QMessageBox.critical(self.window, "错误", "请输入正确的训练数据读取路径")
        elif len(model_savepath) == 0 or not os.path.isdir(model_savepath):
            QMessageBox.critical(self.window, "错误", "请输入正确的模型存储路径")
        else:
            # 当获取路径正常时，就会启动模型训练功能
            # 训练模型是很耗时的动作，会卡死界面
            # 因此将该动作放入子线程中启动
            try:
                # 为了同时启动多个训练线程
                # 在训练过程中将训练按钮设置为不可使用的状态
                self.window.main_window.pushButton_3.setEnabled(False)
                # 这里是将超参数设定弹窗也设置为不可启用状态，该弹窗会在弹窗部分详细说明
                self.window.main_window.menu.setEnabled(False)
                self.train_thread = Thread(target=self._simple_train, args=(train_datapath, model_savepath))
                self.train_thread.start()
            except Exception:
                self.train_thread = None
                self.window.main_window.pushButton_3.setEnabled(True)
                QMessageBox.critical(self.window, "错误", "启动训练失败")
            
	# 这个方法是提供一个关闭主界面时安全退出训练子线程的接口
    def abort(self):
        self.abort_flag = True
        if self.train_thread is not None:
            self.train_thread.join()


"""------------------以下为pytorch训练模型的代码----------------------------------------"""
"""----------------------------------------------------------------------------------"""
"""----------------------------------------------------------------------------------"""

    @staticmethod
    def _train_one_epoch(model, criterion, optimizer, data_loader, epoch, device=torch.device('cpu'), print_freq=1):
        """
        单轮次训练

        参数
            model       网络模型
            criterion   网络模型的损失函数
            optimizer   网络模型优化器
            data_loader 数据装载器,提供训练数据
            epoch       当前训练轮次
            device      选择模型计算的硬件,例如torch.device('cuda') or torch.device('cpu')
            print_freq  训练过程中信息打印的频次,单位是batch,默认是1,也就是每个batch打印一次

        返回
            list(batch_loss1, batch_loss2, ...) 本轮次每个batch的损失值是一个list
        """
        model.train()
        running_losses = []
        epoch_losses = []
        batch_length = len(data_loader)

        print(f'Epoch: {epoch} ->')
        for batch, (x1, x2, y) in enumerate(data_loader, 1):
            x1, x2, y = x1.to(device), x2.to(device), y.to(device)
            output1, output2 = model(x1, x2)
            loss = criterion(output1, output2, y)

            optimizer.zero_grad()
            loss.backward()
            optimizer.step()

            running_losses.append(loss.item())
            epoch_losses.append(loss.item())

            if batch % print_freq == 0:
                print(f'Epoch: {epoch} - Batch: {batch}/{batch_length} - loss: {np.mean(running_losses):.5f}')
                running_losses.clear()
        
        print(f'Epoch: {epoch} - Train average loss: {np.mean(epoch_losses):.5f}')
        return epoch_losses


    @staticmethod
    def _validation_one_epoch(model, criterion, data_loader, epoch, device=torch.device('cpu')):
        """
        单轮次验证

        参数
            model       网络模型
            criterion   网络模型的损失函数
            data_loader 数据装载器,提供验证数据
            epoch       当前训练轮次
            device      选择模型计算的硬件,例如torch.device('cuda') or torch.device('cpu')

        返回
            list(batch_loss1, batch_loss2, ...) 本轮次每个batch的损失值是一个list
        """
        model.eval()
        epoch_losses = []

        for x1, x2, y in data_loader:
            x1, x2, y = x1.to(device), x2.to(device), y.to(device)

            # 验证的时候不需要更新权重
            with torch.no_grad():
                output1, output2 = model(x1, x2)
                loss = criterion(output1, output2, y)
                epoch_losses.append(loss.item())

        print(f'Epoch: {epoch} - Validation average loss: {np.mean(epoch_losses):.5f}')
        return epoch_losses


    def _simple_train(self, train_datasetpath, save_model_dir):
        """
        网络训练
        """
        # 参数设置
        epochs        = self.hyper_param.epochs
        batchsize     = self.hyper_param.batch_size
        learning_rate = self.hyper_param.learning_rate
        print_freq    = 10
        model_name    = 'CRRC'
        pattern       = '*.png'

        # CPU还是GPU
        device = torch.device('cuda') if torch.cuda.is_available() else torch.device('cpu')
        print(f'训练设备：{device}')

        # 训练数据和验证数据预处理
        transform = transforms.Compose([transforms.ToTensor()])
        train_dataset, val_dataset = SiameseNetDataset.train_and_val(train_datasetpath, transform=transform, pattern=pattern, key=lambda x: x.split('_')[0], validation_split=0.1)
        train_dataloader = DataLoader(train_dataset, num_workers=1, batch_size=batchsize, shuffle=True)
        val_dataloader = DataLoader(val_dataset, num_workers=1, batch_size=batchsize, shuffle=False)

        # 网络初始化
        model     = SiameseNet().to(device)
        criterion = ContrastiveLoss()
        optimizer = optim.Adam(model.parameters(), lr=learning_rate)

        print('开始训练')
        print(f'计划训练轮次(Epochs)：{epochs}')
        print(f'计划小批量大小(BatchSize)：{batchsize}')
        print(f'计划学习速率(Learning Rat)：{learning_rate}')

        # 训练
        log = defaultdict(list)
        for epoch in range(1, epochs+1):
            epoch_trainlosses = TrainControl._train_one_epoch(model, criterion, optimizer, train_dataloader, epoch, device, print_freq)
            epoch_vallosses   = TrainControl._validation_one_epoch(model, criterion, val_dataloader, epoch, device)
            log['train_loss'].append(np.mean(epoch_trainlosses))
            log['val_loss'].append(np.mean(epoch_vallosses))

            # 增加中断标志
            if self.abort_flag == True:
                break

        # 模型存储路径处理
        timestamp = time.strftime('%Y%m%d%H%M%S', time.localtime())
        save_model_subdir = os.path.join(save_model_dir, model_name)
        if not os.path.exists(save_model_subdir):
            os.makedirs(save_model_subdir)

        if self.abort_flag == True:
            save_model_name     = f'{model_name}_model_ep{epochs}_{timestamp}_abort.pth'
            save_model_dictpath = os.path.join(save_model_subdir, save_model_name)
        else:
            save_model_name     = f'{model_name}_model_ep{epochs}_{timestamp}.pth'
            save_model_dictpath = os.path.join(save_model_subdir, save_model_name)
            
        # 模型保存
        torch.save(model.state_dict(), save_model_dictpath)

        # 启用训练按钮
        self.window.main_window.pushButton_3.setEnabled(True)
        return log
```

##### 控制台显示

Ui_MainWindow.textBrowser是QT提供的文本框控件，在本次功能设计中，该文本框将用于显示控制台信息。

该部分是复制的代码，具体含义没有深刻了解

```Python
# 设定一个写入文本就执行的信号
class EmittingStr(QObject):
    textWritten = pyqtSignal(str)  #定义一个发送str的信号
    def write(self, text):
        self.textWritten.emit(str(text))


class ConsoleBoard:

    def __init__(self, window):
        # 将标准输出重定向到文本框中
        sys.stdout = EmittingStr(textWritten=self.outputWritten)
        sys.stderr = EmittingStr(textWritten=self.outputWritten)
        self.window = window

	# 操控文本框将信息输入到文本框中
    def outputWritten(self, text):
        cursor = self.window.main_window.textBrowser.textCursor()
        cursor.movePosition(QTextCursor.End)
        cursor.insertText(text)
        self.window.main_window.textBrowser.setTextCursor(cursor)
        self.window.main_window.textBrowser.ensureCursorVisible()
```

##### 菜单超参数设定弹窗

该功能实例化了一个弹窗，该弹窗和主界面是独立的。

```python
class MenuAction:

    def __init__(self, window, hyper_param):
        self.window = window
        # 将菜单的工作和弹窗功能绑定
        self.window.main_window.actionEpochs.triggered.connect(self.messageBox)
        self.hyper_param = hyper_param


    def messageBox(self):
        # 实例化弹窗界面QDialog
        self.dialog = QDialog()
        self.ui_dialog = Ui_Dialog()
        self.ui_dialog.setupUi(self.dialog)

        self.dialog.setWindowTitle("超参数设置")
        
        # 获取当前超参数
        self.ui_dialog.lineEdit_2.setText(str(self.hyper_param.epochs))
        self.ui_dialog.lineEdit_3.setText(str(self.hyper_param.batch_size))
        self.ui_dialog.lineEdit.setText(str(self.hyper_param.learning_rate))

        # 设置超参数
        self.ui_dialog.pushButton_2.clicked.connect(self.set_epochs)
        self.ui_dialog.pushButton_3.clicked.connect(self.set_batch_size)
        self.ui_dialog.pushButton.clicked.connect(self.set_learning_rate)

        #设置窗口的属性为ApplicationModal模态，用户只有关闭弹窗后，才能关闭主界面
        self.dialog.setWindowModality(Qt.ApplicationModal)
        self.dialog.exec_()


    def set_epochs(self):
        epochs = self.ui_dialog.lineEdit_2.text()
        try:
            epochs = int(epochs)
            self.hyper_param.epochs = epochs
            QMessageBox.information(self.window, "消息", "Epochs设置成功!")
        except Exception:
            QMessageBox.critical(self.window, "错误", "输入有误，设置失败!\n请输入整数!")


    def set_batch_size(self):
        batch_size = self.ui_dialog.lineEdit_3.text()
        try:
            batch_size = int(batch_size)
            self.hyper_param.batch_size = batch_size
            QMessageBox.information(self.window, "消息", "batch Size设置成功!")
        except Exception:
            QMessageBox.critical(self.window, "错误", "输入有误，设置失败!\n请输入整数!\n建议为2的倍数!")


    def set_learning_rate(self):
        learning_rate = self.ui_dialog.lineEdit.text()
        try:
            learning_rate = float(learning_rate)
            self.hyper_param.learning_rate = learning_rate
            QMessageBox.information(self.window, "消息", "Learning Rate设置成功!")
        except Exception:
            QMessageBox.critical(self.window, "错误", "输入有误，设置失败!\n请输入浮点数!")
```

##### 超参数容器

因为超参数设定功能实现，需要联系MenuAction和TrainControl两个类，为了不直接在主类中增加太多属性，设计了一个超参数容器类在传递数据。

```python
class HyperParamContainer:

    def __init__(self):
        self.defaut()


    def defaut(self):
        self.epochs        = 20
        self.batch_size    = 32
        self.learning_rate = 1e-4
```

#### 完成状态的入口类

将所有类在该类中实例化，并添加退出检查

```python
class TrainModelUi(QMainWindow):

    def __init__(self):
        super().__init__()

        hyper_param = HyperParamContainer()

        self.main_window = Ui_MainWindow()
        self.main_window.setupUi(self) 

        self.console_board = ConsoleBoard(self)
        self.datadir_control = DataDirControl(self)
        self.modeldir_control = ModelDirControl(self)

        self.menuAction = MenuAction(self, hyper_param)
        self.train_control = TrainControl(self, hyper_param)

        self.show()


    def closeEvent(self, event):
        # 主界面退出检查，通过train_control对象的终止接口，在退出时正常终止训练成程序
        reply = QMessageBox.question(self, '退出',
            "是否退出?", QMessageBox.Yes | 
            QMessageBox.No, QMessageBox.No)

        if reply == QMessageBox.Yes:
            self.train_control.abort()
            event.accept()
        else:
            event.ignore()
```

