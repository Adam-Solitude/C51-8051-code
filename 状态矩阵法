#include <reg51.h>
#include<intrins.h>
#define uchar unsigned char
#define uint unsigned int
#define out P2
#define outk P1 
#define cs P0 

uchar key;                   // 按键译码值       
uchar SUB_flag = 8;          // 子程序标志位，程序初始化时8位数码管全暗
uchar PREST = 0;             // 现态
uchar NXTST;                 // 下态
uchar OFS_flag = 0;          // 偏移标志位

void delayms(uint);
uchar scan(void);            // 按键扫描(编码)子程序，中断方式,逐行扫描
void status_monitor(void);   // 状态矩阵法监控程序
void shuma_display(void);
void LED_display(uchar LED_8,uchar LED_7,uchar LED_6,uchar LED_5,uchar LED_4,uchar LED_3,uchar LED_2,uchar LED_1);
// [全暗,a,b,[,d,e,f,s,t,g,r,-,],u,o,1,2,3,4] 共阳
uchar code seg[]={0xff,0x88,0x83,0xc6,0xa1,0x86,0x8e,0x92,0xce,0x90,0xaf,0xbf,0xf0,0xc1,0xc0,0xf9,0xa4,0xb0,0x99};

void main(void)
{
   IE = 0X81;  		        // 开总中断和外中断INT0
   TCON &= 0xfd;            // INT0电平触发

   while(1)
	{
       outk = 0xf0;		     // 扫描所有行，判断是否有按键
       shuma_display();	
	}
}

void delayms(uint j)
{
uchar i;
for(;j>0;j--)
  	{
	 i=250;
   	 while(--i);
   	 i=249;
   	 while(--i);
   	}
}

uchar scan(void)
{
  uchar k=16,m,n,in;

  for(m=0;m<4;m++)
  {
	  outk=~(0x01<<m);        
	  for(n=0;n<4;n++)
     {
		  in=outk;
		  in=in>>(4+n);
	     if((in&0x01)==0)
			 {
			   delayms(10);
			   if((in&0x01)==0){k=n+m*4;break;}
			 }
     }
	  if(k!=16){break;}
  }
	  return(k);
}

void ex_int0() interrupt 0
{
  uchar i;
  key=scan();                 // 识别键码
  status_monitor();           // 状态监控
  for(i=0;i<60;i++)
  {
     shuma_display();
  }
  //delayms(200);             // 等待按键结束(一次按键大约500ms)
  TCON &= 0xfd;               // 清零中断标志位IE0
}

void status_monitor(void) 
{
  if (PREST==0)
  {  
    switch(key)
	 {
		case 0:  NXTST=3; SUB_flag=40;      // A     
		         break;
	  case 1:  NXTST=3; SUB_flag=41;      // B
						 break;
    case 2:  NXTST=3; SUB_flag=42;      // C           
						 break;
	  case 3:  NXTST=3; SUB_flag=43;      // D             
				     break;
		case 4:  NXTST=1; SUB_flag=1;       // SET 
				     break;
		case 5:  NXTST=2; SUB_flag=2;       // GATE            
 	           break;						 
    case 6:  NXTST=3; SUB_flag=5;       // OFS  
		         OFS_flag++; 
				     if(OFS_flag == 2)
                 OFS_flag = 0;   
 	           break;
    case 7:  NXTST=3; SUB_flag=3;      // RESET    
 	           break;
		default: NXTST=0;       		   // *
		         break;
	 }	
  }
  else if (PREST==1)
  {
    switch(key)
	 {
	   case 0:  NXTST=0; SUB_flag=60;       
		          break;
	   case 1:  NXTST=0; SUB_flag=61;   
			      	break;
     case 2:  NXTST=0; SUB_flag=62;                
			      	break;
	   case 3:  NXTST=0; SUB_flag=63;                     
			      	break;
		 case 8:  NXTST=4; SUB_flag=98;
		 		  	break;
	   default: NXTST=0;           
		          break;   
	 }
  }
  else if (PREST==2)
  {
    switch(key)
	 {
     case 0:  NXTST=0; SUB_flag=70;           
							break;
	   case 1:  NXTST=0; SUB_flag=71;       
							break;
     case 2:  NXTST=0; SUB_flag=72;                
							break;
	   case 3:  NXTST=0; SUB_flag=73;                     
							break;
	   default: NXTST=0;          
		          break;   
	 }
  }
  else if (PREST==3)// PREST==3
  {
       NXTST=0; 
  }
	else if (PREST==4)
	{
		switch(key)
	 {
     case 0:  NXTST=0; SUB_flag=90;           
							break;
	   case 1:  NXTST=0; SUB_flag=91;       
							break;
     case 2:  NXTST=0; SUB_flag=92;                
							break;
	   case 3:  NXTST=0; SUB_flag=93;                     
							break;
	   default: NXTST=0;          
		          break;   
	 }
	}
       

  if (PREST==0)      // 0态为不稳定态，自动再查一次状态表
  {  
    switch(key)
	 {
		  case 0:  PREST=3; SUB_flag=40;          
		           break;
	    case 1:  PREST=3; SUB_flag=41;      
				       break;
      case 2:  PREST=3; SUB_flag=42;                  
				       break;
	    case 3:  PREST=3; SUB_flag=43;                     
				       break;
		  case 4:  PREST=1; SUB_flag=1;                    
				       break;
	  	case 5:  PREST=2; SUB_flag=2;                     
 	             break;
      case 6:  PREST=3; SUB_flag=5;                
 	             break;
      case 7:  PREST=3; SUB_flag=3;              
 	             break;
	 	  default: PREST=3;      
		           break;
	 }	
  }
  PREST = NXTST;
}


void shuma_display(void)
{
    switch(SUB_flag)
    {
     case 1:  LED_display(0,0,0,0,0,7,5,8);      // 显示 " SET "       
		          break;
	   case 2:  LED_display(0,0,0,0,9,1,8,5);      // 显示 " GATE "
				      break;
     case 3:  LED_display(0,0,0,10,5,7,5,8);     // 显示 " RESET "         
				      break;
	   case 40: LED_display(0,0,0,1,11,3,6,12);    // A键显示 " A-[F] "          
				      break;
	   case 41: LED_display(0,0,0,2,11,3,8,12);    // B键显示 " B-[T] "
		          break;
	   case 42: LED_display(0,3,11,3,8,1,2,12);    // C键显示 " C-[TAB] "
				      break;
     case 43: LED_display(0,0,0,4,11,3,13,12);   // D键显示 " D-[U] " 
				      break;
	   case 60: LED_display(0,0,0,7,5,8,11,1);     // A键显示 " SET-A "       
				      break;
	   case 61: LED_display(0,0,0,7,5,8,11,2);     // B键显示 " SET-B "
		          break;
	   case 62: LED_display(0,0,0,7,5,8,11,3);     // C键显示 " SET-C "
				      break;
     case 63: LED_display(0,0,0,7,5,8,11,4);     // D键显示 " SET-D "         
				      break;
     case 70: LED_display(0,0,9,1,8,5,11,1);     // A键显示 " GATE-A "          
				      break;
	   case 71: LED_display(0,0,9,1,8,5,11,2);     // B键显示 " GATE-B "
		          break;
	   case 72: LED_display(0,0,9,1,8,5,11,3);     // C键显示 " GATE-C "
				      break;
     case 73: LED_display(0,0,9,1,8,5,11,4);     // D键显示 " GATE-D "
				      break;
     case 5:  if(OFS_flag == 1)
		         {LED_display(0,0,0,14,6,7,11,15);} // 显示 " OFS-1 "
      			  else 
					   {LED_display(0,0,0,14,6,7,11,16);} // 显示 " OFS-2 " 
				     break;
		 case 98: LED_display(0,0,0,0,0,16,17,16);
						 break;
		 case 90: LED_display(0,0,0,16,17,16,11,15);
						 break;
		case 91: LED_display(0,0,0,16,17,16,11,16);
						 break;
		case 92: LED_display(0,0,0,16,17,16,11,17);
						 break;
	 case 93: LED_display(0,0,0,16,17,16,11,18);
						 break;
		default: cs=0x00;                           // 都不显示
		         break; 
    }
	 PREST = NXTST;
}

void LED_display(uchar LED_8,uchar LED_7,uchar LED_6,uchar LED_5,uchar LED_4,uchar LED_3,uchar LED_2,uchar LED_1)
{
    cs=0x80;            // 点亮第8位
    out=seg[LED_8];
    delayms(1);
    cs=0x40;            // 点亮第7位
    out=seg[LED_7];
	  delayms(1);
	  cs=0x20;            // 点亮第6位
    out=seg[LED_6];
	  delayms(1);
	  cs=0x10;            // 点亮第5位
    out=seg[LED_5];
	  delayms(1);
	  cs=0x08;            // 点亮第4位
    out=seg[LED_4];
	  delayms(1);
	  cs=0x04;            // 点亮第3位
    out=seg[LED_3];
	  delayms(1);
	  cs=0x02;            // 点亮第2位
    out=seg[LED_2];
	  delayms(1);
	  cs=0x01;            // 点亮第1位
    out=seg[LED_1];
	  delayms(1);
}
