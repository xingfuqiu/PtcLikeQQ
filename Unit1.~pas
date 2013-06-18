unit Unit1;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, AppEvnts, StdCtrls, ExtCtrls,jpeg, ExtDlgs, Glass;

type
TScreenState = (msDefault,msDrag,msSelected);
  TForm1 = class(TForm)
    ImgScreen: TImage;
    ApplicationEvents1: TApplicationEvents;
    SavePictureDialog1: TSavePictureDialog;
    Glass1: TGlass;
    LblRGB: TLabel;
    LblCancelInfo: TLabel;
    LblActionInfo: TLabel;
    Label1: TLabel;
    procedure FormCreate(Sender: TObject);
    procedure ApplicationEvents1Message(var Msg: tagMSG;
      var Handled: Boolean);
    procedure ImgScreenDblClick(Sender: TObject);
    procedure ImgScreenMouseDown(Sender: TObject; Button: TMouseButton;
      Shift: TShiftState; X, Y: Integer);
    procedure ImgScreenMouseMove(Sender: TObject; Shift: TShiftState; X,
      Y: Integer);
    procedure ImgScreenMouseUp(Sender: TObject; Button: TMouseButton;
      Shift: TShiftState; X, Y: Integer);
    procedure FormShow(Sender: TObject);
    procedure Glass1MouseMove(Sender: TObject; Shift: TShiftState; X,
      Y: Integer);
  private
    { Private declarations }

    DX,DY,RectLeft,RectTop,RectBottom,RectRight:Integer;
    MouseIsDown,
    Trace:Boolean;
    ScreenState:TScreenState;
    procedure Cancel;
    procedure SendImg;
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.FormShow(Sender: TObject);
var
  Fullscreen:Tbitmap;
  FullscreenCanvas:TCanvas;
  DC:HDC;
begin
    ScreenState:=msDefault;
    MouseIsDown:=False;
    Trace:=False;
    RectLeft:=-1;
    RectTop:=-1;
    RectBottom:=-1;
    RectRight:=-1;

    Fullscreen := TBitmap.Create;//创建一个BITMAP来存放图象
    Fullscreen.Width := Screen.width;
    Fullscreen.Height := Screen.Height;
    DC:=GetDC(0);//取得屏幕的DC，参数0指的是屏幕
    FullscreenCanvas := TCanvas.Create;//创建一个CANVAS对象
    FullscreenCanvas.Handle := DC;
    Fullscreen.Canvas.CopyRect(Rect(0,0,Screen.Width,Screen.Height),FullscreenCanvas,
    Rect(0,0,Screen.Width,Screen.Height));//把整个屏幕复制到BITMAP中
    FullscreenCanvas.Free;//释放CANVAS对象
    ReleaseDC(0,DC);//释放DC
    ImgScreen.picture.Bitmap:=fullscreen;//拷贝下的图象赋给Image1
    ImgScreen.Width := Fullscreen.Width;
    ImgScreen.Height:=Fullscreen.Height;
    Fullscreen.free;//释放bitmap

    ImgScreen.Canvas.Pen.mode:=pmnot; //笔的模式为取反
    ImgScreen.canvas.pen.color:=clblack; //笔为黑色
    ImgScreen.canvas.pen.Width:=2;         //笔宽度
    ImgScreen.canvas.brush.Style:=bsclear; //空白刷子end;
end;

procedure TForm1.FormCreate(Sender: TObject);
begin
  Self.DoubleBuffered:=True;
end;

procedure TForm1.Cancel;
begin
  if ScreenState=msDefault then
    Close 
  else
  begin
    if Trace then ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
    Trace:=False;
    ScreenState:=msDefault;
    LblActionInfo.Caption:='按住鼠标左键不放选择截取范围';
    LblCancelInfo.Caption:='按鼠标右键退出';
    exit;
  end;
end;

procedure TForm1.ImgScreenMouseMove(Sender: TObject;
  Shift: TShiftState; X, Y: Integer);
var
  R,G,B:Integer;
begin
  //判断确保鼠标在pninfo内
  if (X>Glass1.Left) and (X<Glass1.Left+Glass1.Width) and (Y>Glass1.Top) and (Y<Glass1.Top+Glass1.Height) then
  begin
    Glass1MouseMove(Sender,Shift,X,Y);
  end;


  if (ScreenState=msSelected) then
  begin
    ImgScreen.Cursor:=crDefault;
        //判断鼠标是否在画出的矩形内
    if (X>RectLeft) and (X<RectRight) and (Y>RectTop) and (Y<RectBottom) then
    begin
      ImgScreen.Cursor:=crSizeAll;
      if MouseIsDown then
      begin
        ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
        if (RectLeft+(X-DX)>=1) and (RectRight+(X-DX)<=Screen.Width) then
        begin
          RectLeft:=RectLeft+(X-DX);
          RectRight:=RectRight+(X-DX);
        end;

        if (RectTop+(Y-DY)>=1) and (RectBottom+(Y-DY)<=Screen.Height) then
        begin
          RectTop:=RectTop+(Y-DY);
          RectBottom:=RectBottom+(Y-DY);
        end;
        
        ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
        DX:=X;
        DY:=Y;
      end;
    end;
  end
  else
  begin
    ImgScreen.Cursor:=crCross;
  end;

  if (ScreenState=msDrag) and MouseIsDown then
  begin
    if Trace then ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
    RectRight:=X;
    RectBottom:=Y;
    ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
    Trace:=True;
  end;
  
  R:=getRvalue(ImgScreen.Canvas.Pixels[X, Y]);
  G:=getGvalue(ImgScreen.Canvas.Pixels[X, Y]);
  B:=getBvalue(ImgScreen.Canvas.Pixels[X, Y]);
  LblRGB.Caption:='当前像素RGB值（'+IntToStr(R)+'、'+IntToStr(G)+'、'+IntToStr(B)+'）';
end;

procedure TForm1.ImgScreenMouseDown(Sender: TObject;
  Button: TMouseButton; Shift: TShiftState; X, Y: Integer);
begin
  if Button=mbRight then
  begin
    Cancel;
    Exit;
  end;

  if (ScreenState=msSelected) and (ImgScreen.Cursor<>crDefault) then
  begin
    MouseIsDown:=True;
    DX:=X;
    DY:=Y;
  end;

  if ScreenState<>msDefault then exit;
  if Trace then ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
  MouseIsDown:=True;
  Trace:=False;
  ScreenState:=msDrag;
  RectLeft:=X;
  RectTop:=Y;
  RectRight:=X;
  RectBottom:=Y;
  LblActionInfo.Caption:='松开鼠标左键以确定最终截取范围';
  LblCancelInfo.Caption:='按鼠标右键取消当前选区';
end;

procedure TForm1.ImgScreenMouseUp(Sender: TObject;
  Button: TMouseButton; Shift: TShiftState; X, Y: Integer);
begin
    MouseIsDown:=False;
    if ScreenState=msDrag then
    begin
      LblActionInfo.Caption:='双击左键选择当前选区的图像';
      LblCancelInfo.Caption:='按鼠标右键取消当前选区';
      ScreenState:=msSelected;
    end;
end;

procedure TForm1.SendImg;
var
  newbitmap:TBitmap;
  newjpg:TJPegImage;
  TempInt:Integer;
  ScreenFileName:String;
  Receiver:Integer;
  ReceiverName:String;
begin
  if ScreenState=msSelected then
  begin
    //判断截图拖动方式，防止图片翻转
    if RectLeft>RectRight then
    begin
      TempInt:=RectLeft;
      RectLeft:=RectRight;
      RectRight:=TempInt;
    end;
    
    if RectTop>RectBottom then
    begin
      TempInt:=RectTop;
      RectTop:=RectBottom;
      RectBottom:=TempInt;
    end;

    //Dec(RectRight);
    //Dec(RectBottom);


    newbitmap:=Tbitmap.create;
    newbitmap.width:=RectRight-RectLeft;
    newbitmap.height:=RectBottom-RectTop;
    if Trace then ImgScreen.Canvas.Rectangle(RectLeft,RectTop,RectRight,RectBottom);
    newbitmap.Canvas.CopyRect(Rect(0, 0, newbitmap.width, newbitmap.height),ImgScreen.canvas,Rect (RectLeft, RectTop,RectRight,RectBottom)); //拷贝
    
    newjpg:=TJPegImage.Create;
    newjpg.Assign(newbitmap);          //转换图片格式
    newjpg.CompressionQuality:=90;  //图片压缩质量
    newjpg.Compress;                //图片压缩
    ScreenFileName:=ExtractFilePath(Application.ExeName)+'\Screens\SC'+IntToStr(GetTickCount)+'.JPG';
    if not DirectoryExists(ExtractFilePath(ScreenFileName)) then CreateDir(ExtractFilePath(ScreenFileName)); //目录不存在则新建
    newjpg.SaveToFile(ScreenFileName);         //保存成文件
    newjpg.Free;
    newbitmap.free;

    ScreenState:=msDefault;
    Close;
  end;
end;

procedure TForm1.ApplicationEvents1Message(var Msg: tagMSG;
  var Handled: Boolean);
begin
  if Msg.wParam=27 then     
  begin
    Cancel;
  end;

  if (Msg.wParam=32) or (Msg.wParam=13) then
  begin
    if ScreenState=msSelected then SendImg;
    Handled:=True;
  end;
end;

procedure TForm1.ImgScreenDblClick(Sender: TObject);
begin
    if ScreenState=msSelected then SendImg;
end;

procedure TForm1.Glass1MouseMove(Sender: TObject; Shift: TShiftState; X,
  Y: Integer);
begin
  if Glass1.Left=8 then
    Glass1.Left:=Screen.Width-8-Glass1.Width
  else
    Glass1.Left:=8;
end;

end.
