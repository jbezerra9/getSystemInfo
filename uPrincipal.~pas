unit uPrincipal;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, StdCtrls, WinSock, ExtCtrls, shellapi, MMSystem, Buttons, DB,
  DBClient;

type
  TForm1 = class(TForm)
    img_info: TImage;
    Timer1: TTimer;
    Label20: TLabel;
    Label21: TLabel;
    Label22: TLabel;
    Label23: TLabel;
    Label24: TLabel;
    Label25: TLabel;
    Label26: TLabel;
    Label27: TLabel;
    Label28: TLabel;
    Label29: TLabel;
    Label30: TLabel;
    Label31: TLabel;
    Label32: TLabel;
    Label33: TLabel;
    Label34: TLabel;
    Label35: TLabel;
    Label36: TLabel;
    Label37: TLabel;
    Label38: TLabel;
    Button1: TButton;
    Label12: TLabel;
    Label13: TLabel;
    procedure FormCreate(Sender: TObject);
    procedure BitBtn1Click(Sender: TObject);
    procedure Timer1Timer(Sender: TObject);
  private
    { Private declarations }
    xama : integer;
    function GetCPUSpeed: Double;
    function Retorna_IP: string;
    function Retorna_Nome: string;
    function Retorna_Dominio: string;
    function Retorna_Usuario: string;
    function Retorna_Memoria: string;
    procedure info(s1, s2: string);
    function Percentdisk(unidade: byte): String;
    function Li2Double(x: LARGE_INTEGER): Double;
    procedure GetCPUUsage;
    procedure GetRAMUsage;
  public
    { Public declarations }
  end;

var
  Form1: TForm1;
  gn_speed_y: Integer;
  gn_text_y: Integer;

const
  gn_speed_x: Integer = 8;
  gn_text_x: Integer = 15;
  gl_start: Boolean = True;
  SystemBasicInformation = 0;
  SystemPerformanceInformation = 2;
  SystemTimeInformation = 3;

type
     TPDWord = ^DWORD;

     TSystem_Basic_Information = packed record
     dwUnknown1: DWORD;
     uKeMaximumIncrement: ULONG;
     uPageSize: ULONG;
     uMmNumberOfPhysicalPages: ULONG;
     uMmLowestPhysicalPage: ULONG;
     uMmHighestPhysicalPage: ULONG;
     uAllocationGranularity: ULONG;
     pLowestUserAddress: Pointer;
     pMmHighestUserAddress: Pointer;
     uKeActiveProcessors: ULONG;
     bKeNumberProcessors: byte;
     bUnknown2: byte;
     wUnknown3: word;
end;
type
     TSystem_Performance_Information = packed record
     liIdleTime: LARGE_INTEGER; {LARGE_INTEGER}
     dwSpare: array[0..75] of DWORD;
end;

type
     TSystem_Time_Information = packed record
     liKeBootTime: LARGE_INTEGER;
     liKeSystemTime: LARGE_INTEGER;
     liExpTimeZoneBias: LARGE_INTEGER;
     uCurrentTimeZoneId: ULONG;
     dwReserved: DWORD;
end;

var
     NtQuerySystemInformation: function(infoClass: DWORD;
     buffer: Pointer;
     bufSize: DWORD;
     returnSize: TPDword): DWORD; stdcall = nil;


     liOldIdleTime: LARGE_INTEGER = ();
     liOldSystemTime: LARGE_INTEGER = ();



implementation

{$R *.dfm}

function GetRAMUsage : String;
var
     MemoryStatus : TMemoryStatus;

begin
	Result := '';

     MemoryStatus.dwLength:= sizeof(MemoryStatus);
     GlobalMemoryStatus(MemoryStatus);

     Result := FloatToStr(MemoryStatus.dwMemoryLoad) + '%';
end;

procedure TForm1.GetCPUUsage;
var
     SysBaseInfo: TSystem_Basic_Information;
     SysPerfInfo: TSystem_Performance_Information;
     SysTimeInfo: TSystem_Time_Information;
     status: Longint; {long}
     dbSystemTime: Double;
     dbIdleTime: Double;

     bLoopAborted : boolean;

begin
  {   if @NtQuerySystemInformation = nil then
     NtQuerySystemInformation := GetProcAddress(GetModuleHandle('ntdll.dll'),
     'NtQuerySystemInformation');

     // get number of processors in the system

     status := NtQuerySystemInformation(SystemBasicInformation, @SysBaseInfo, SizeOf(SysBaseInfo), nil);
     if status <> 0 then Exit;

     bLoopAborted := False;

     while not bLoopAborted do
     begin
          // get new system time
          status := NtQuerySystemInformation(SystemTimeInformation, @SysTimeInfo, SizeOf(SysTimeInfo), 0);
          if status <> 0 then Exit;

          // get new CPU's idle time
          status := NtQuerySystemInformation(SystemPerformanceInformation, @SysPerfInfo, SizeOf(SysPerfInfo), nil);
          if status <> 0 then Exit;

          // if it's a first call - skip it
          if (liOldIdleTime.QuadPart <> 0) then
          begin

               // CurrentValue = NewValue - OldValue
               dbIdleTime := Li2Double(SysPerfInfo.liIdleTime) - Li2Double(liOldIdleTime);
               dbSystemTime := Li2Double(SysTimeInfo.liKeSystemTime) - Li2Double(liOldSystemTime);

               // CurrentCpuIdle = IdleTime / SystemTime
               dbIdleTime := dbIdleTime / dbSystemTime;

               // CurrentCpuUsage% = 100 - (CurrentCpuIdle * 100) / NumberOfProcessors
               dbIdleTime := 100.0 - dbIdleTime * 100.0 / SysBaseInfo.bKeNumberProcessors + 0.5;

              { if(AllocMemCount > 0)then
               	Label12.Caption := IntToStr(AllocMemCount)
               else
               	Label12.Caption := 'Nulo';

               // Show Percentage
             //  Form1.Label10.Caption := FormatFloat('0.0 %',dbIdleTime);

               Application.ProcessMessages;

               // Abort if user pressed ESC or Application is terminated
               bLoopAborted := Application.Terminated;

          end;

          // store new CPU's idle and system time
          liOldIdleTime := SysPerfInfo.liIdleTime;
          liOldSystemTime := SysTimeInfo.liKeSystemTime;

          // wait one second
          Sleep(1000);
	end;      }
end;

function TForm1.Li2Double(x: LARGE_INTEGER): Double;
begin
	Result := x.HighPart * 4.294967296E9 + x.LowPart
end;

function TForm1.GetCPUSpeed: Double;
const
  DelayTime = 500;
var
  TimerHi, TimerLo: DWORD;
  PriorityClass, Priority: Integer;
begin
  try
    PriorityClass := GetPriorityClass(GetCurrentProcess);
    Priority := GetThreadPriority(GetCurrentThread);
    SetPriorityClass(GetCurrentProcess, REALTIME_PRIORITY_CLASS);
    SetThreadPriority(GetCurrentThread, THREAD_PRIORITY_TIME_CRITICAL);
    Sleep(10);
    asm
      dw 310Fh // rdtsc
      mov TimerLo, eax
      mov TimerHi, edx
    end;
    Sleep(DelayTime);
    asm
      dw 310Fh // rdtsc
      sub eax, TimerLo
      sbb edx, TimerHi
      mov TimerLo, eax
      mov TimerHi, edx
    end;
    SetThreadPriority(GetCurrentThread, Priority);
    SetPriorityClass(GetCurrentProcess, PriorityClass);
    Result := TimerLo / (1000.0 * DelayTime);
  except
  end;
end;

function TForm1.Retorna_IP: string;
var
  p: PHostEnt;
  s: array[0..128] of char;
  p2: pchar;
  wVersionRequested: WORD;
  wsaData: TWSAData;
begin
  wVersionRequested := MAKEWORD(1, 1);
  WSAStartup(wVersionRequested, wsaData);
  GetHostName(@s, 128);
  p := GetHostByName(@s);
  p2 := iNet_ntoa(PInAddr(p^.h_addr_list^)^);
  Result := p2;
  WSACleanup;
end;

function TForm1.Retorna_Nome: string;
var
  p: PHostEnt;
  s: array[0..128] of char;
  p2: pchar;
  wVersionRequested: WORD;
  wsaData: TWSAData;
begin
  wVersionRequested := MAKEWORD(1, 1);
  WSAStartup(wVersionRequested, wsaData);
  GetHostName(@s, 128);
  p := GetHostByName(@s);
  Result := p^.h_Name;
end;

function TForm1.Retorna_Dominio: string;
var
  hProcesso, hTokenAcesso: THandle;
  Buffer: PChar;
  Usuario: array[0..31] of char;
  Dominio: array[0..31] of char;
 
  TamanhoBufferInfo: Cardinal;
  TamanhoUsuario: Cardinal;
  TamanhoDominio: Cardinal;
  snu: SID_NAME_USE;
 
begin
  TamanhoBufferInfo := 1000;
  TamanhoUsuario := sizeof(Usuario);
  TamanhoDominio := sizeof(Dominio);
 
  hProcesso := GetCurrentProcess;
  if OpenProcessToken(hProcesso, TOKEN_READ, hTokenAcesso) then
  try
    GetMem(Buffer, TamanhoBufferInfo);
    try
      if GetTokenInformation(hTokenAcesso, TokenUser, Buffer, TamanhoBufferInfo,
        TamanhoBufferInfo) then
        LookupAccountSid(nil, PSIDAndAttributes(Buffer)^.sid, Usuario,
          TamanhoUsuario, Dominio, TamanhoDominio, snu)
      else
        RaiseLastOSError;
    finally
      FreeMem(Buffer);
    end;
    result := Dominio;
  finally
    CloseHandle(hTokenAcesso);
  end
end;

function TForm1.Retorna_Usuario: string;
var
  cUser: array[0..144] of Char;
  BufferSize: DWord;
  cUserName: string;
 
begin
  BufferSize := SizeOf(cUser);
  GetUserName(cUser, BufferSize);
  cUserName := Trim(StrPas(cUser));
  Result := cUserName;
end;

function TForm1.Retorna_Memoria: string;
var
  MemoryStatus: TMemoryStatus;
begin
  MemoryStatus.dwLength := sizeof(MemoryStatus);
  GlobalMemoryStatus(MemoryStatus);
  Result := 'Total de memória física : ' + FormatFloat('#0,00',MemoryStatus.dwTotalPhys);
  (*
  {typedef struct _MEMORYSTATUS}
  DWORD dwLength; // sizeof(MEMORYSTATUS)
  DWORD dwMemoryLoad; // percentual de memória em uso
  DWORD dwTotalPhys; // bytes de memória física
  DWORD dwAvailPhys; // bytes livres de memória física
  DWORD dwTotalPageFile; // bytes de paginação de arquivo
  DWORD dwAvailPageFile; // bytes livres de paginação de arquivo
  DWORD dwTotalVirtual; // bytes em uso de espaço de endereço
  DWORD dwAvailVirtual; // bytes livres}
  *)
end;

procedure TForm1.FormCreate(Sender: TObject);
var
  cpuspeed: string;
  _eax, _ebx, _ecx, _edx: Longword;
  i: Integer;
  b: Byte;
  b1: Word;
  s, s1, s2, s3, s_all: string;
begin
  {cpuspeed := '¬f MHz ' + FloatToStr(GetCPUSpeed);
  Label1.Caption := 'Nome máquina: ' + Retorna_Nome;
  Label2.Caption := 'IP: ' + Retorna_IP;
  Label3.Caption := 'Domínio: ' + Retorna_Dominio;
  Label4.Caption := 'Velocidade do CPU: ' + cpuspeed +
    ' (valor aproximado)';
  Label5.Caption := 'Nome do usuário na rede: ' + Retorna_Usuario;
  Label6.Caption := 'Memória RAM: ' + Retorna_Memoria + ' (em bytes)';
  Label8.Caption := 'Tamanho do Disco: ' + FormatFloat('#0,000',DiskSize(0) div 1024) + ' (em bytes)';
  Label7.Caption := 'Espaço Livre: ' + FormatFloat('0,000',DiskFree(0) div 1024) + ' (em bytes)';

    // Set the startup colour of the image
  img_info.Canvas.Brush.Color := clblue;
  img_info.Canvas.FillRect(rect(0, 0, img_info.Width, img_info.Height));
  gn_text_y := 5; // position of the 1st text
  asm                // asm call to the CPUID inst.
    mov eax,0         // sub. func call
    db $0F,$A2         // db $0F,$A2 = CPUID instruction
    mov _ebx,ebx
    mov _ecx,ecx
    mov _edx,edx
  end;
  for i := 0 to 3 do // extract vendor id
  begin
    b := lo(_ebx);
    s := s + chr(b);
    b := lo(_ecx);
    s1 := s1 + chr(b);
    b := lo(_edx);
    s2 := s2 + chr(b);
    _ebx := _ebx shr 8;
    _ecx := _ecx shr 8;
    _edx := _edx shr 8;
  end;
  info('CPU', '');
  info('   - ' + 'Vendor ID: ', s + s2 + s1);
  asm
    mov eax,1
    db $0F,$A2
    mov _eax,eax
    mov _ebx,ebx
    mov _ecx,ecx
    mov _edx,edx
  end;
  // 06B1
  // |0000| |0000 0000| |0000| |00| |00| |0110| |1011| |0001|
  b := lo(_eax) and 15;
  info('   - ' + 'Stepping ID: ', IntToStr(b));
  b := lo(_eax) shr 4;
  info('   - ' + 'Model Number: ', IntToHex(b, 1));
  b := hi(_eax) and 15;
  info('   - ' + 'Family Code: ', IntToStr(b));
  b := hi(_eax) shr 4;
  info('   - ' + 'Processor Type: ', IntToStr(b));
  // 31.   28. 27.   24. 23.   20. 19.   16.
  // 0 0 0 0   0 0 0 0   0 0 0 0   0 0 0 0
  b := lo((_eax shr 16)) and 15;
  info('   - ' + 'Extended Model: ', IntToStr(b));
  b := lo((_eax shr 20));
  info('   - ' + 'Extended Family: ', IntToStr(b));
  b := lo(_ebx);
  info('   - ' + 'Brand ID: ', IntToStr(b));
  b := hi(_ebx);
  info('   - ' + 'Chunks: ', IntToStr(b));
  b := lo(_ebx shr 16);
  info('   - ' + 'Count: ', IntToStr(b));
  b := hi(_ebx shr 16);
  info('   - ' + 'APIC ID: ', IntToStr(b));
  // Bit 18 =? 1     //is serial number enabled?
  if (_edx and $40000) = $40000 then
    info('   - ' + 'Serial Number ', 'Enabled')
  else
    info('   - ' + 'Serial Number ', 'Disabled');
  s := IntToHex(_eax, 8);
  asm                  // determine the serial number
    mov eax,3
    db $0F,$A2
    mov _ecx,ecx
    mov _edx,edx
  end;
  s1 := IntToHex(_edx, 8);
  s2 := IntToHex(_ecx, 8);
  Insert('-', s, 5);
  Insert('-', s1, 5);
  Insert('-', s2, 5);
  info('   - ' + 'Serial Number: ', s + '-' + s1 + '-' + s2);
  asm
    mov eax,1
    db $0F,$A2
    mov _edx,edx
  end;
  info('', '');
  // Bit 23 =? 1
  if (_edx and $800000) = $800000 then
    info('MMX ', 'Supported')
  else
    info('MMX ', 'Not Supported');
  // Bit 24 =? 1
  if (_edx and $01000000) = $01000000 then
    info('FXSAVE & FXRSTOR Instructions ', 'Supported')
  else
    info('FXSAVE & FXRSTOR Instructions Not ', 'Supported');
  // Bit 25 =? 1
  if (_edx and $02000000) = $02000000 then
    info('SSE ', 'Supported')
  else
    info('SSE ', 'Not Supported');
  // Bit 26 =? 1
  if (_edx and $04000000) = $04000000 then
    info('SSE2 ', 'Supported')
  else
    info('SSE2 ', 'Not Supported');
  info('', '');
  asm     // execute the extended CPUID inst.
    mov eax,$80000000   // sub. func call
    db $0F,$A2
    mov _eax,eax
  end;
  if _eax > $80000000 then // any other sub. funct avail. ?
  begin
    info('Extended CPUID: ', 'Supported');
    info('   - Largest Function Supported: ', IntToStr(_eax - $80000000));
    asm     // get brand ID
      mov eax,$80000002
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    s_all := s3 + s + s1 + s2;
    asm
      mov eax,$80000003
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    s_all := s_all + s3 + s + s1 + s2;
    asm
      mov eax,$80000004
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    info('Brand String: ', '');
    if s2[Length(s2)] = #0 then
      setlength(s2, Length(s2) - 1);
    info('', '   - ' + s_all + s3 + s + s1 + s2);
  end
  else
    info('   - Extended CPUID ', 'Not Supported.');

      info('Tamanho do Disco: ', FormatFloat('#0,000',DiskSize(0) div 1024) + ' (em bytes)');
  	 info('Espaço Livre: ' , FormatFloat('0,000',DiskFree(0) div 1024) + ' (em bytes)'); }
end;

procedure TForm1.info(s1, s2: string);
begin
  if s1 <> '' then
  begin
    img_info.Canvas.Brush.Color := clblue;
    img_info.Canvas.Font.Color := clyellow;
    img_info.Canvas.TextOut(gn_text_x, gn_text_y, s1);
  end;
  if s2 <> '' then
  begin
    img_info.Canvas.Brush.Color := clblue;
    img_info.Canvas.Font.Color := clWhite;
    img_info.Canvas.TextOut(gn_text_x + img_info.Canvas.TextWidth(s1),
      gn_text_y, s2);
  end;
  Inc(gn_text_y, 13);
end;

function TForm1.Percentdisk(unidade: byte): String;
{Retorna a porcentagem de espaço livre em uma unidade de disco}
var
	A,B, Percentual : longint;
begin
	if DiskFree(Unidade) = -1 then
	begin
          A := DiskFree(Unidade) div 1024;
          B := DiskSize(Unidade) div 1024;
          Percentual:=(A*100) div B;
          result := IntToStr(Percentual);
	end
     else
     begin
          result := IntToStr(-1);
	end;
end;
procedure TForm1.BitBtn1Click(Sender: TObject);
begin
	GetCPUUsage;
end;

procedure TForm1.Timer1Timer(Sender: TObject);
var
     MemoryStatus: TMemoryStatus;
     cpuspeed: string;
     _eax, _ebx, _ecx, _edx: Longword;
     i: Integer;
     b: Byte;
     b1: Word;
     s, s1, s2, s3, s_all: string;
     SysBaseInfo: TSystem_Basic_Information;
     SysPerfInfo: TSystem_Performance_Information;
     SysTimeInfo: TSystem_Time_Information;
     status: Longint; {long}
     dbSystemTime: Double;
     dbIdleTime: Double;

     bLoopAborted : boolean;
begin
      //retornos em mega
      MemoryStatus.dwLength:= sizeof(MemoryStatus);
      GlobalMemoryStatus(MemoryStatus);
      Label20.Caption := FloatToStr((MemoryStatus.dwTotalPhys/1024)/1024) + ' MB';
      Label21.Caption := FloatToStr(((MemoryStatus.dwTotalPhys - MemoryStatus.dwAvailPhys)/1024)/1024) + ' MB';
      Label22.Caption := FloatToStr((MemoryStatus.dwAvailPhys/1024)/1024) + ' MB';

      Label26.Caption := FloatToStr((MemoryStatus.dwTotalPageFile/1024)/1024) + ' MB gay';
      Label27.Caption := FloatToStr(((MemoryStatus.dwTotalPageFile - MemoryStatus.dwAvailPageFile)/1024)/1024) + ' MB';
      Label28.Caption := FloatToStr((MemoryStatus.dwAvailPageFile/1024)/1024) + ' MB';

      Label32.Caption := FloatToStr((MemoryStatus.dwAvailVirtual/1024)/1024) + ' MB';
      Label33.Caption := FloatToStr((MemoryStatus.dwTotalVirtual/1024)/1024) + ' MB';
      Label34.Caption := FloatToStr(((MemoryStatus.dwTotalVirtual + MemoryStatus.dwAvailVirtual)/1024)/1024) + ' MB';

      //retornos de uso de memoria em %
      Label37.Caption := FloatToStr(100 - MemoryStatus.dwMemoryLoad) + '%';
      Label38.Caption := FloatToStr(MemoryStatus.dwMemoryLoad) + '%';

 { cpuspeed := '¬f MHz ' + FloatToStr(GetCPUSpeed);
 // Label1.Caption := 'Nome máquina: ' + Retorna_Nome;
 // Label2.Caption := 'IP: ' + Retorna_IP;
  Label3.Caption := 'Domínio: ' + Retorna_Dominio;
  Label4.Caption := 'Velocidade do CPU: ' + cpuspeed +
    ' (valor aproximado)';
  Label5.Caption := 'Nome do usuário na rede: ' + Retorna_Usuario;
  Label6.Caption := 'Memória RAM: ' + Retorna_Memoria + ' (em bytes)';
 // Label8.Caption := 'Tamanho do Disco: ' + FormatFloat('#0,000',DiskSize(0) div 1024 div 1024) + ' (em mega)';
 // Label7.Caption := 'Espaço Livre: ' + FormatFloat('0,000',DiskFree(0) div 1024) + ' (em bytes)';  }

    // Set the startup colour of the image
  img_info.Canvas.Brush.Color := clblue;
  img_info.Canvas.FillRect(rect(0, 0, img_info.Width, img_info.Height));
  gn_text_y := 5; // position of the 1st text
  asm                // asm call to the CPUID inst.
    mov eax,0         // sub. func call
    db $0F,$A2         // db $0F,$A2 = CPUID instruction
    mov _ebx,ebx
    mov _ecx,ecx
    mov _edx,edx
  end;
  for i := 0 to 3 do // extract vendor id
  begin
    b := lo(_ebx);
    s := s + chr(b);
    b := lo(_ecx);
    s1 := s1 + chr(b);
    b := lo(_edx);
    s2 := s2 + chr(b);
    _ebx := _ebx shr 8;
    _ecx := _ecx shr 8;
    _edx := _edx shr 8;
  end;
  info('CPU', '');
  info('   - ' + 'Vendor ID: ', s + s2 + s1);
  asm
    mov eax,1
    db $0F,$A2
    mov _eax,eax
    mov _ebx,ebx
    mov _ecx,ecx
    mov _edx,edx
  end;
  // 06B1
  // |0000| |0000 0000| |0000| |00| |00| |0110| |1011| |0001|
  b := lo(_eax) and 15;
  info('   - ' + 'Stepping ID: ', IntToStr(b));
  b := lo(_eax) shr 4;
  info('   - ' + 'Model Number: ', IntToHex(b, 1));
  b := hi(_eax) and 15;
  info('   - ' + 'Family Code: ', IntToStr(b));
  b := hi(_eax) shr 4;
  info('   - ' + 'Processor Type: ', IntToStr(b));
  // 31.   28. 27.   24. 23.   20. 19.   16.
  // 0 0 0 0   0 0 0 0   0 0 0 0   0 0 0 0
  b := lo((_eax shr 16)) and 15;
  info('   - ' + 'Extended Model: ', IntToStr(b));
  b := lo((_eax shr 20));
  info('   - ' + 'Extended Family: ', IntToStr(b));
  b := lo(_ebx);
  info('   - ' + 'Brand ID: ', IntToStr(b));
  b := hi(_ebx);
  info('   - ' + 'Chunks: ', IntToStr(b));
  b := lo(_ebx shr 16);
  info('   - ' + 'Count: ', IntToStr(b));
  b := hi(_ebx shr 16);
  info('   - ' + 'APIC ID: ', IntToStr(b));
  // Bit 18 =? 1     //is serial number enabled?
  if (_edx and $40000) = $40000 then
    info('   - ' + 'Serial Number ', 'Enabled')
  else
    info('   - ' + 'Serial Number ', 'Disabled');
  s := IntToHex(_eax, 8);
  asm                  // determine the serial number
    mov eax,3
    db $0F,$A2
    mov _ecx,ecx
    mov _edx,edx
  end;
  s1 := IntToHex(_edx, 8);
  s2 := IntToHex(_ecx, 8);
  Insert('-', s, 5);
  Insert('-', s1, 5);
  Insert('-', s2, 5);
  info('   - ' + 'Serial Number: ', s + '-' + s1 + '-' + s2);
  asm
    mov eax,1
    db $0F,$A2
    mov _edx,edx
  end;
  info('', '');
  // Bit 23 =? 1
  if (_edx and $800000) = $800000 then
    info('MMX ', 'Supported')
  else
    info('MMX ', 'Not Supported');
  // Bit 24 =? 1
  if (_edx and $01000000) = $01000000 then
    info('FXSAVE & FXRSTOR Instructions ', 'Supported')
  else
    info('FXSAVE & FXRSTOR Instructions Not ', 'Supported');
  // Bit 25 =? 1
  if (_edx and $02000000) = $02000000 then
    info('SSE ', 'Supported')
  else
    info('SSE ', 'Not Supported');
  // Bit 26 =? 1
  if (_edx and $04000000) = $04000000 then
    info('SSE2 ', 'Supported')
  else
    info('SSE2 ', 'Not Supported');
  info('', '');
  asm     // execute the extended CPUID inst.
    mov eax,$80000000   // sub. func call
    db $0F,$A2
    mov _eax,eax
  end;
  if _eax > $80000000 then // any other sub. funct avail. ?
  begin
    info('Extended CPUID: ', 'Supported');
    info('   - Largest Function Supported: ', IntToStr(_eax - $80000000));
    asm     // get brand ID
      mov eax,$80000002
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    s_all := s3 + s + s1 + s2;
    asm
      mov eax,$80000003
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    s_all := s_all + s3 + s + s1 + s2;
    asm
      mov eax,$80000004
      db $0F
      db $A2
      mov _eax,eax
      mov _ebx,ebx
      mov _ecx,ecx
      mov _edx,edx
    end;
    s := '';
    s1 := '';
    s2 := '';
    s3 := '';
    for i := 0 to 3 do
    begin
      b := lo(_eax);
      s3 := s3 + chr(b);
      b := lo(_ebx);
      s := s + chr(b);
      b := lo(_ecx);
      s1 := s1 + chr(b);
      b := lo(_edx);
      s2 := s2 + chr(b);
      _eax := _eax shr 8;
      _ebx := _ebx shr 8;
      _ecx := _ecx shr 8;
      _edx := _edx shr 8;
    end;
    info('Brand String: ', '');
    if s2[Length(s2)] = #0 then
      setlength(s2, Length(s2) - 1);
    info('', '   - ' + s_all + s3 + s + s1 + s2);
  end
  else
    info('   - Extended CPUID ', 'Not Supported.');

    	 info('Nome máquina: ' , Retorna_Nome);
      info('IP: ' , Retorna_IP);
      info('Memoria RAM em uso: ', FloatToStr(MemoryStatus.dwMemoryLoad) + '%');
      info('Memoria RAM TOTAL: ', FloatToStr(MemoryStatus.dwTotalPageFile div 1024 div 1024 div 1000));
      info('Tamanho do Disco: ' , FormatFloat('#0,000',DiskSize(0) div 1024 div 1024) + ' Gb');
      info('Espaço Livre: ' , FormatFloat('0,000',DiskFree(0) div 1024 div 1024) + ' Gb');

           if @NtQuerySystemInformation = nil then
     NtQuerySystemInformation := GetProcAddress(GetModuleHandle('ntdll.dll'),
     'NtQuerySystemInformation');

     // get number of processors in the system

     status := NtQuerySystemInformation(SystemBasicInformation, @SysBaseInfo, SizeOf(SysBaseInfo), nil);
     if status <> 0 then Exit;

     bLoopAborted := False;

          // get new system time
          status := NtQuerySystemInformation(SystemTimeInformation, @SysTimeInfo, SizeOf(SysTimeInfo), 0);
          if status <> 0 then Exit;

          // get new CPU's idle time
          status := NtQuerySystemInformation(SystemPerformanceInformation, @SysPerfInfo, SizeOf(SysPerfInfo), nil);
          if status <> 0 then Exit;

          // if it's a first call - skip it
          if (liOldIdleTime.QuadPart <> 0) then
          begin
               // CurrentValue = NewValue - OldValue
               dbIdleTime := Li2Double(SysPerfInfo.liIdleTime) - Li2Double(liOldIdleTime);
               dbSystemTime := Li2Double(SysTimeInfo.liKeSystemTime) - Li2Double(liOldSystemTime);

               // CurrentCpuIdle = IdleTime / SystemTime
               dbIdleTime := dbIdleTime / dbSystemTime;

               // CurrentCpuUsage% = 100 - (CurrentCpuIdle * 100) / NumberOfProcessors
               dbIdleTime := 100.0 - dbIdleTime * 100.0 / SysBaseInfo.bKeNumberProcessors + 0.5;

              { if(AllocMemCount > 0)then
               	Label12.Caption := IntToStr(AllocMemCount)
               else
               	Label12.Caption := 'Nulo';
               }
               // Show Percentage
               info('CPU em uso: ', FormatFloat('0.0 %',dbIdleTime));

               Application.ProcessMessages;

               // Abort if user pressed ESC or Application is terminated
               bLoopAborted := Application.Terminated;

          end;

        // store new CPU's idle and system time
          liOldIdleTime := SysPerfInfo.liIdleTime;
          liOldSystemTime := SysTimeInfo.liKeSystemTime;

        // wait one second
          Sleep(1000);


end;

end.
