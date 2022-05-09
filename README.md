# 2048_vs2022
#include<stdio.h>
#include<stdlib.h>
#include<Windows.h>
#include<conio.h>
#include<time.h>

#define LEFT 75
#define RIGHT 77
#define UP 72
#define DOWN 80
#define ESC 27

#define ZERO 256 // 0
#define TWO 240 // 2
#define FOUR 112 // 4
#define EIGHT 128 // 8
#define SIXTEEN 176 // 16
#define THIRTYTOW 48 // 32
#define SIXTYFOUR 144 // 64
#define HUNDREDTWENTYEIGHT 272 // 128
#define TWOHUNDREDFIFTYSIX 80 // 256
#define FIVEHUNDREDTWELVE 80 // 512
#define THOUSANDTWENTYFOUR 64 // 1024
#define TWOTHOUSANDFORTYEIGHT 64 // 2048

#define ZERO_ 256 // 0 배경색
#define TWO_ 255 // 2 배경색
#define FOUR_ 119 // 4 배경색
#define EIGHT_ 136 // 8 배경색
#define SIXTEEN_ 187 // 16 배경색
#define THIRTYTOW_ 51 // 32 배경색
#define SIXTYFOUR_ 153 // 64 배경색
#define HUNDREDTWENTYEIGHT_ 273 // 128 배경색
#define TWOHUNDREDFIFTYSIX_ 85 // 256 배경색
#define FIVEHUNDREDTWELVE_ 85 // 512 배경색
#define THOUSANDTWENTYFOUR_ 68 // 1024 배경색
#define TWOTHOUSANDFORTYEIGHT_ 68 // 2048 배경색


int screen[16] =
{
 0,0,0,0,
 0,0,0,0,
 0,0,0,0,
 0,0,0,0
};
// 0: 비어있음, 2,4,8,16,....: 그 숫자로 저장, 후에 계산할 때 그대로 계산

int width[4] = { 0,0,0,0 };//각 세로줄 (왼쪽부터 순서대로) 확인 여부, 0: 확인해야함, 1: 확인할 필요 없음
int length[4] = { 0,0,0,0 };// 각 가로줄 (위쪽부터 순서대로) 확인 여부, 0: 확인해야함, 1: 확인할 필요 없음
int check_appear = 1;//움직였는지 움직이지 않았는지 체크 (움직였을 때 1, 움직이지 않았을 때 0 )
int screen_type = 0;//종료 취소시 이전 화면 저장할 변수, (0: 없음, 1: 게임오버화면, 2: 메인화면, 3: 게임 도중)
int continue_num = 0;//일시정지 후 계속하기를 택하였을 경우 appear가 한번 더 반복되는 경우를 막기위한 변수

void gotoxy(int x, int y);//커서이동(완)
void CursorView(char show);//커서숨기기(완)
void color(int color_num);//글자색 설정(완)
void print_start_screen();//화면출력
void active();//모든 활동의 중심, 게임 진행하는 함수
void print_screen();//현재 screen 출력, 모든 계산이 끝난 뒤에 출력할 때 사용함
void appear();//블럭 생성, 랜덤한 위치(space), 랜덤한 수(num 1또는 2 (홀수, 짝수)) 입력받고 화면에 출력------2 또는 4 출력, 벽 또는 블럭 있는 곳 제외
void main_screen();//메인화면을 띄운다 (게임시작, 종료, 프로그램 정보 포함)
void main_finish_screen();//메인화면에서 종료할 경우 다시묻기
void finish_screen();//종료 여부를 다시 묻는다, 그리고 종료, 또는 원래 화면으로 돌아가기
void end_screen();//모든 작동을 멈추고 엔딩을 띄운다
void end_stop();//게임도중 일시정지 화면, 게임으로 돌아갈지 종료할지 묻는다
void information_screen();//게임정보, 도움말 화면


void check_up();//위로 가기 확인, 계산
void check_down();//아래로가기 확인, 계산
void check_left();//왼쪽가기 확인, 계산
void check_right();//오른쪽가기 확인, 계산
void move_up(int a);//위로 움직이기
void move_down(int b);//아래로 움직이기
void move_left(int c);//왼쪽으로 움직이기
void move_right(int d);//오른쪽으로 움직이기

int check_end();//더이상 게임을 진행할 수 있는지 체크(진행할 수 있으면 1, 없으면 0 반환)

void screen_test();//스크린 문제 테스트하기
void check_bug(); //버그찾기(2,4,8...을 제외한 다른 숫자가 나올경우)

void gotoxy(int x, int y)
{
	COORD Pos;
	Pos.X = x;
	Pos.Y = y;
	SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), Pos);
}

void CursorView(char show)//커서숨기기(완)
{
	HANDLE hConsole;
	CONSOLE_CURSOR_INFO ConsoleCursor;

	hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

	ConsoleCursor.bVisible = show;
	ConsoleCursor.dwSize = 1;

	SetConsoleCursorInfo(hConsole, &ConsoleCursor);
}

void color(int color_num)
{
	SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color_num);
}

void print_start_screen()
{
	system("mode con cols=80 lines=30");
	color(238);
	for (int i = 0; i <= 30; i++)
	{
		for (int l = 0; l <= 80; l++)
		{
			gotoxy(l, i);
			printf("■");
		}
	}
	
	color(102);
	for (int i = 5; i <= 16; i++)
	{
		for (int l = 10; l <= 32; l = l + 2)
		{
			gotoxy(l, i);
			printf("■");
		}
	}

	color(256);
	for (int i = 7; i <= 14; i++)
	{
		for (int l = 14; l <= 28; l = l + 2)
		{
			gotoxy(l, i);
			printf("■");
		}
	}
	gotoxy(20, 10);
}

void active()
{
	char ch;
	int check_num = 0;

	//end_screen();
	
	while (1)//블럭 옮기기 한 바퀴
	{
		if (check_end() == 0)
		{
			//color(232);
			//gotoxy(37, 4);
			//printf("게임오버");
			end_screen();
		}

		if (check_num % 2 == 0 && check_appear == 1)
		{
			if (continue_num == 0)
			{
				appear();//블럭 랜덤한 위치, 숫자로 생성
				//gotoxy(50, 5);
				//color(232);
				//printf("숫자생성");

				continue_num = 0;
			}
		}
		//color(232);
		//gotoxy(50, 4);
		//printf("확인: %d", check_num);//함수 반복 횟수 확인

		//screen_test();//화면 출력 테스트
		print_screen();//화면출력

		ch = _getch();
		switch (ch)
		{
		case UP:
			check_up(); break;
		case DOWN:
			check_down(); break;
		case LEFT:
			check_left(); break;
		case RIGHT:
			check_right(); break;
		case ESC:
			end_stop();
		}

		//color(232);
		//gotoxy(50, 5);
		//printf("        ");//숫자생성 글씨 지우기

		//screen_test();//화면 출력 테스트
		print_screen();//화면출력

		if (check_end() == 0)
		{
			//color(232);
			//gotoxy(37, 4);
			//printf("게임오버");
			end_screen();
		}
		check_num++;
		//check_bug();//오류 체크
		Sleep(100);
	}
}

void print_screen()
{
	for (int fst = 0; fst <= 3; fst++)//1번째 줄 출력
	{
		switch (screen[fst])//숫자없는 배경색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO_); break;
		case 4:
			color(FOUR_); break;
		case 8:
			color(EIGHT_); break;
		case 16:
			color(SIXTEEN_); break;
		case 32:
			color(THIRTYTOW_); break;
		case 64:
			color(SIXTYFOUR_); break;
		case 128:
			color(HUNDREDTWENTYEIGHT_); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX_); break;
		case 512:
			color(FIVEHUNDREDTWELVE_); break;
		case 1024:
			color(THOUSANDTWENTYFOUR_); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT_); break;
		}
		gotoxy(14 + (fst * 4), 7);
		printf("0000");

		switch (screen[fst])//숫자 색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO); break;
		case 4:
			color(FOUR); break;
		case 8:
			color(EIGHT); break;
		case 16:
			color(SIXTEEN); break;
		case 32:
			color(THIRTYTOW); break;
		case 64:
			color(SIXTYFOUR); break;
		case 128:
			color(HUNDREDTWENTYEIGHT); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX); break;
		case 512:
			color(FIVEHUNDREDTWELVE); break;
		case 1024:
			color(THOUSANDTWENTYFOUR); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT); break;
		}

		if (screen[fst] < 10)//1자리수인 경우
		{
			gotoxy(14 + (fst * 4), 8);
			printf("  %d ", screen[fst]);
		}
		else if (screen[fst] < 100)//2자리수인 경우
		{
			gotoxy(14 + (fst * 4), 8);
			printf(" %d ", screen[fst]);
		}
		else if (screen[fst] < 1000)//3자리수인 경우
		{
			gotoxy(14 + (fst * 4), 8);
			printf(" %d", screen[fst]);
		}
		else if (screen[fst] < 10000)//4자리수인 경우
		{
			gotoxy(14 + (fst * 4), 8);
			printf("%d", screen[fst]);
		}
	}

	for (int scd = 0; scd <= 3; scd++)//2번째 줄 출력
	{
		switch (screen[scd + 4])//숫자없는 배경색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO_); break;
		case 4:
			color(FOUR_); break;
		case 8:
			color(EIGHT_); break;
		case 16:
			color(SIXTEEN_); break;
		case 32:
			color(THIRTYTOW_); break;
		case 64:
			color(SIXTYFOUR_); break;
		case 128:
			color(HUNDREDTWENTYEIGHT_); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX_); break;
		case 512:
			color(FIVEHUNDREDTWELVE_); break;
		case 1024:
			color(THOUSANDTWENTYFOUR_); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT_); break;
		}
		gotoxy(14 + (scd * 4), 9);
		printf("0000");

		switch (screen[scd + 4])//숫자 색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO); break;
		case 4:
			color(FOUR); break;
		case 8:
			color(EIGHT); break;
		case 16:
			color(SIXTEEN); break;
		case 32:
			color(THIRTYTOW); break;
		case 64:
			color(SIXTYFOUR); break;
		case 128:
			color(HUNDREDTWENTYEIGHT); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX); break;
		case 512:
			color(FIVEHUNDREDTWELVE); break;
		case 1024:
			color(THOUSANDTWENTYFOUR); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT); break;
		}

		if (screen[scd + 4] < 10)//1자리수인 경우
		{
			gotoxy(14 + (scd * 4), 10);
			printf("  %d ", screen[scd + 4]);
		}
		else if (screen[scd + 4] < 100)//2자리수인 경우
		{
			gotoxy(14 + (scd * 4), 10);
			printf(" %d ", screen[scd + 4]);
		}
		else if (screen[scd + 4] < 1000)//3자리수인 경우
		{
			gotoxy(14 + (scd * 4), 10);
			printf(" %d", screen[scd + 4]);
		}
		else if (screen[scd + 4] < 10000)//4자리수인 경우
		{
			gotoxy(14 + (scd * 4), 10);
			printf("%d", screen[scd + 4]);
		}
	}

	for (int trd = 0; trd <= 3; trd++)//3번째 줄 출력
	{
		switch (screen[trd + 8])//숫자없는 배경색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO_); break;
		case 4:
			color(FOUR_); break;
		case 8:
			color(EIGHT_); break;
		case 16:
			color(SIXTEEN_); break;
		case 32:
			color(THIRTYTOW_); break;
		case 64:
			color(SIXTYFOUR_); break;
		case 128:
			color(HUNDREDTWENTYEIGHT_); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX_); break;
		case 512:
			color(FIVEHUNDREDTWELVE_); break;
		case 1024:
			color(THOUSANDTWENTYFOUR_); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT_); break;
		}
		gotoxy(14 + (trd * 4), 11);
		printf("0000");

		switch (screen[trd + 8])//숫자 색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO); break;
		case 4:
			color(FOUR); break;
		case 8:
			color(EIGHT); break;
		case 16:
			color(SIXTEEN); break;
		case 32:
			color(THIRTYTOW); break;
		case 64:
			color(SIXTYFOUR); break;
		case 128:
			color(HUNDREDTWENTYEIGHT); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX); break;
		case 512:
			color(FIVEHUNDREDTWELVE); break;
		case 1024:
			color(THOUSANDTWENTYFOUR); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT); break;
		}

		if (screen[trd + 8] < 10)//1자리수인 경우
		{
			gotoxy(14 + (trd * 4), 12);
			printf("  %d ", screen[trd + 8]);
		}
		else if (screen[trd + 8] < 100)//2자리수인 경우
		{
			gotoxy(14 + (trd * 4), 12);
			printf(" %d ", screen[trd + 8]);
		}
		else if (screen[trd + 8] < 1000)//3자리수인 경우
		{
			gotoxy(14 + (trd * 4), 12);
			printf(" %d", screen[trd + 8]);
		}
		else if (screen[trd + 8] < 10000)//4자리수인 경우
		{
			gotoxy(14 + (trd * 4), 12);
			printf("%d", screen[trd + 8]);
		}
	}

	for (int fth = 0; fth <= 3; fth++)//4번째 줄 출력
	{
		switch (screen[fth + 12])//숫자없는 배경색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO_); break;
		case 4:
			color(FOUR_); break;
		case 8:
			color(EIGHT_); break;
		case 16:
			color(SIXTEEN_); break;
		case 32:
			color(THIRTYTOW_); break;
		case 64:
			color(SIXTYFOUR_); break;
		case 128:
			color(HUNDREDTWENTYEIGHT_); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX_); break;
		case 512:
			color(FIVEHUNDREDTWELVE_); break;
		case 1024:
			color(THOUSANDTWENTYFOUR_); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT_); break;
		}
		gotoxy(14 + (fth * 4), 13);
		printf("0000");

		switch (screen[fth + 12])//숫자 색
		{
		case 0:
			color(ZERO); break;
		case 2:
			color(TWO); break;
		case 4:
			color(FOUR); break;
		case 8:
			color(EIGHT); break;
		case 16:
			color(SIXTEEN); break;
		case 32:
			color(THIRTYTOW); break;
		case 64:
			color(SIXTYFOUR); break;
		case 128:
			color(HUNDREDTWENTYEIGHT); break;
		case 256:
			color(TWOHUNDREDFIFTYSIX); break;
		case 512:
			color(FIVEHUNDREDTWELVE); break;
		case 1024:
			color(THOUSANDTWENTYFOUR); break;
		case 2048:
			color(TWOTHOUSANDFORTYEIGHT); break;
		}

		if (screen[fth + 12] < 10)//1자리수인 경우
		{
			gotoxy(14 + (fth * 4), 14);
			printf("  %d ", screen[fth + 12]);
		}
		else if (screen[fth + 12] < 100)//2자리수인 경우
		{
			gotoxy(14 + (fth * 4), 14);
			printf(" %d ", screen[fth + 12]);
		}
		else if (screen[fth + 12] < 1000)//3자리수인 경우
		{
			gotoxy(14 + (fth * 4), 14);
			printf(" %d", screen[fth + 12]);
		}
		else if (screen[fth + 12] < 10000)//4자리수인 경우
		{
			gotoxy(14 + (fth * 4), 14);
			printf("%d", screen[fth + 12]);
		}
	}


	/*
	int a = 0;
	for (int i = 4; i <= 7; i++)
	{
		for (int l = 8; l <= 14; l = l + 2)
		{
			gotoxy(l, i);
			if (screen[a] == 0)
			{
				color(256);
				printf("■");
			}
			else
			{
				switch (screen[a])
				{
				case 2:
					color(TWO); break;
				case 4:
					color(FOUR); break;
				case 8:
					color(EIGHT); break;
				case 16:
					color(SIXTEEN); break;
				case 32:
					color(THIRTYTOW); break;
				case 64:
					color(SIXTYFOUR); break;
				case 128:
					color(HUNDREDTWENTYEIGHT); break;
				case 256:
					color(TWOHUNDREDFIFTYSIX); break;
				case 1024:
					color(THOUSANDTWENTYFOUR); break;
				case 2048:
					color(TWOTHOUSANDFORTYEIGHT); break;
				}
				if (screen[a] < 10)
				{
					printf("%d ", screen[a]);
				}
				else if (screen[a] >= 10)
				{
					printf("%d", screen[a]);
				}
			}
			a++;
		}
	}*/
}

void appear()//블럭 생성, 랜덤한 위치(space), 랜덤한 수(num 1또는 0 (홀수, 짝수) 결정 후 화면에 출력------2 또는 4 출력, 벽 또는 블럭 있는 곳 제외
{
	srand(time(NULL));
	int space = rand() % 2;//블럭 나올 모양 정하기
	int num = rand() % 16;//화면중 랜덤한 위치 정하기
	int place = 2;

	if (space == 0)
		place = 2;
	else if (space == 1)
		place = 4;

	while (1)
	{
		if (screen[num] != 0)
		{
			num = rand() % 16;
		}
		else if (screen[num] == 0)
		{
			screen[num] = place;
			break;
		}
	}
}

void main_screen()
{
	char ch_main;
	int main_y = 8;

	color(238);
	for (int i = 0; i <= 30; i++)
	{
		for (int l = 0; l <= 80; l++)
		{
			gotoxy(l, i);
			printf("■");
		}
	}
	color(233);
	gotoxy(30, 4);
	printf("◈");
	gotoxy(17, 4);
	printf("◈");
	gotoxy(21, 8);
	printf("새 게임");
	color(224);
	gotoxy(21, 4);
	printf("2 0 4 8");
	gotoxy(21, 10);
	printf("도움말");
	gotoxy(21, 12);
	printf("종료");
	color(233);
	gotoxy(18, main_y);
	printf("▶");
	color(235);
	gotoxy(16, 16);
	printf("선택: [스페이스바]");

	while (1)
	{
		ch_main = _getch();
		switch (ch_main)
		{
		case UP:
		{
			color(224);
			gotoxy(18, main_y);
			printf("  ");
			color(224);
			gotoxy(21, 8);
			printf("새 게임");
			gotoxy(21, 10);
			printf("도움말");
			gotoxy(21, 12);
			printf("종료");
			if (main_y == 8)
				main_y = 12;
			else
				main_y -= 2;
			break;
		}
		case DOWN:
		{
			color(224);
			gotoxy(18, main_y);
			printf("  ");
			color(224);
			gotoxy(21, 8);
			printf("새 게임");
			gotoxy(21, 10);
			printf("도움말");
			gotoxy(21, 12);
			printf("종료");
			if (main_y == 12)
				main_y = 8;
			else
				main_y += 2;
			break;
		}
		case 32:
		{
			if (main_y == 8)//게임시작
			{
				print_start_screen();
				active();
			}
			else if (main_y == 10)//게임정보
			{
				color(238);
				gotoxy(25, 5);
				printf("            ");
				gotoxy(20, 8);
				printf("            ");
				gotoxy(20, 10);
				printf("            ");
				gotoxy(20, 12);
				printf("            ");
				gotoxy(16, 16);
				printf("                      ");
				gotoxy(14, main_y);
				printf("     ");
				information_screen();
			}
			else if (main_y == 12)//종료
			{
				color(238);
				gotoxy(20, 5);
				printf("              ");
				gotoxy(16, 8);
				printf("              ");
				gotoxy(16, 10);
				printf("              ");
				gotoxy(16, 12);
				printf("              ");
				gotoxy(16, 16);
				printf("                     ");
				gotoxy(16, main_y);
				printf("  ");
				main_finish_screen();
			}
			break;
		}
		}
		color(233);
		gotoxy(18, main_y);
		printf("▶");
		if (main_y == 8)
		{
			gotoxy(21, 8);
			printf("새 게임");
		}
		else if (main_y == 10)
		{
			gotoxy(21, 10);
			printf("도움말");
		}
		else if (main_y == 12)
		{
			gotoxy(21, 12);
			printf("종료");
		}
		//gotoxy(18, 20);
		//printf("%d", main_y);
	}
}

void main_finish_screen()
{
	int main_finish_y = 11;
	char ch_finish;
	color(233);
	gotoxy(18, 11);
	printf("▶");
	gotoxy(21, 11);
	printf("예");
	color(224);
	gotoxy(17, 9);
	printf("종료 하시겠습니까?");
	gotoxy(20, 13);
	printf("아니오");

	while (1)
	{
		ch_finish = _getch();

		switch (ch_finish)
		{
		case UP:
		{
			gotoxy(18, main_finish_y);
			printf("  ");
			if (main_finish_y == 11)
				main_finish_y = 13;
			else if (main_finish_y == 13)
				main_finish_y = 11;
			break;
		}
		case DOWN:
		{
			gotoxy(18, main_finish_y);
			printf("  ");
			if (main_finish_y == 11)
				main_finish_y = 13;
			else if (main_finish_y == 13)
				main_finish_y = 11;
			break;
		}
		case 32:
		{
			if (main_finish_y == 11)
			{
				exit(0);
			}
			else if (main_finish_y == 13)
			{
				main_screen();
			}
		}
		}
		color(233);
		gotoxy(18, main_finish_y);
		printf("▶");
		if (main_finish_y == 11)
		{
			gotoxy(21, 11);
			printf("예");
			color(224);
			gotoxy(20, 13);
			printf("아니오");
		}
		else if (main_finish_y == 13)
		{
			gotoxy(20, 13);
			printf("아니오");
			color(224);
			gotoxy(21, 11);
			printf("예");
		}
	}
}

void finish_screen()
{
	int finish_y = 11;
	char ch_finish;
	color(233);
	gotoxy(43, 11);
	printf("▶");
	gotoxy(55, 11);
	printf("◀");
	gotoxy(43, 9);
	printf("종료 하시겠습니까?");
	gotoxy(48, 11);
	printf("예");
	gotoxy(47, 13);
	printf("아니오");

	while (1)
	{
		ch_finish = _getch();

		switch (ch_finish)
		{
		case UP:
		{
			gotoxy(43, finish_y);
			printf("  ");
			gotoxy(55, finish_y);
			printf("  ");
			if (finish_y == 11)
				finish_y = 13;
			else if (finish_y == 13)
				finish_y = 11;
			break;
		}
		case DOWN:
		{
			gotoxy(43, finish_y);
			printf("  ");
			gotoxy(55, finish_y);
			printf("  ");
			if (finish_y == 11)
				finish_y = 13;
			else if (finish_y == 13)
				finish_y = 11;
			break;
		}
		case 32:
		{
			if (finish_y == 11)
			{
				exit(0);
			}
			else if (finish_y == 13)
			{
				if (screen_type == 0)// 버그
				{

				}
				else if (screen_type == 1)// 게임오버화면
				{
					screen_type = 0;
					color(238);
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 11);
					printf("                          ");
					gotoxy(38, 13);
					printf("                          ");
					end_screen();
				}
				else if (screen_type == 2)//메인화면
				{

				}
				else if (screen_type == 3)//게임도중
				{

				}
			}
		}
		}
		color(233);
		gotoxy(43, finish_y);
		printf("▶");
		gotoxy(55, finish_y);
		printf("◀");
	}
}

void end_screen()
{
	int arrow_y = 10;
	char ch_end;

	color(233);
	gotoxy(42, 10);
	printf("▶");
	gotoxy(56, 10);
	printf("◀");
	gotoxy(46, 10);
	printf("다시하기");
	gotoxy(46, 12);
	printf("메인화면");
	gotoxy(46, 14);
	printf("게임종료");
	color(235);
	gotoxy(42, 16);
	printf("선택: [스페이스바]");
	while (1)
	{
		color(225);
		gotoxy(38, 6);
		printf("★☆★☆★☆★☆★☆★☆");
		gotoxy(38, 7);
		printf("☆ ＧＡＭＥ  ＯＶＥＲ ★");
		gotoxy(38, 8);
		printf("★☆★☆★☆★☆★☆★☆");
		Sleep(30);
		gotoxy(38, 6);
		printf("☆★☆★☆★☆★☆★☆★");
		gotoxy(38, 7);
		printf("★ ＧＡＭＥ  ＯＶＥＲ ☆");
		gotoxy(38, 8);
		printf("☆★☆★☆★☆★☆★☆★");
		Sleep(30);
		if (_kbhit())
		{
			ch_end = _getch();
			switch (ch_end)
			{
			case UP:
			{
				gotoxy(42, arrow_y);
				printf("  ");
				gotoxy(56, arrow_y);
				printf("  ");
				if (arrow_y == 10)
					arrow_y = 14;
				else
					arrow_y -= 2;
				break;
			}
			case DOWN:
			{
				gotoxy(42, arrow_y);
				printf("  ");
				gotoxy(56, arrow_y);
				printf("  ");
				if (arrow_y == 14)
					arrow_y = 10;
				else
					arrow_y += 2;
				break;
			}
			case 32:
			{
				if (arrow_y == 10)//다시하기
				{
					color(238);
					gotoxy(38, 6);
					printf("                          ");
					gotoxy(38, 7);
					printf("                          ");
					gotoxy(38, 8);
					printf("                          ");
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 10);
					printf("                          ");
					gotoxy(38, 12);
					printf("                          ");
					gotoxy(38, 14);
					printf("                          ");
					gotoxy(38, 16);
					printf("                          ");
					for (int q = 0; q <= 15; q++)
					{
						screen[q] = 0;
					}
					active();
				}
				else if (arrow_y == 12)//메인화면
				{

				}
				else if (arrow_y == 14)//게임종료
				{
					screen_type = 1;
					color(238);
					gotoxy(38, 6);
					printf("                          ");
					gotoxy(38, 7);
					printf("                          ");
					gotoxy(38, 8);
					printf("                          ");
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 10);
					printf("                          ");
					gotoxy(38, 12);
					printf("                          ");
					gotoxy(38, 14);
					printf("                          ");
					gotoxy(38, 16);
					printf("                          ");
					finish_screen();
				}
				break;
			}
			}
		}
		color(233);
		gotoxy(42, arrow_y);
		printf("▶");
		gotoxy(56, arrow_y);
		printf("◀");
		//gotoxy(40, 3);
		//printf("%d", arrow_y);
	}
}

void end_stop()//게임도중 일시정지 화면, 게임으로 돌아갈지 종료할지 묻는다
{
	int arrow_y = 10;
	char ch_end;

	color(233);
	gotoxy(42, 10);
	printf("▶");
	gotoxy(56, 10);
	printf("◀");
	gotoxy(46, 10);
	printf("계속하기");
	gotoxy(46, 12);
	printf("다시하기");
	gotoxy(46, 14);
	printf("게임종료");
	color(235);
	gotoxy(42, 16);
	printf("선택: [스페이스바]");
	while (1)
	{
		if (_kbhit())
		{
			ch_end = _getch();
			switch (ch_end)
			{
			case UP:
			{
				gotoxy(42, arrow_y);
				printf("  ");
				gotoxy(56, arrow_y);
				printf("  ");
				if (arrow_y == 10)
					arrow_y = 14;
				else
					arrow_y -= 2;
				break;
			}
			case DOWN:
			{
				gotoxy(42, arrow_y);
				printf("  ");
				gotoxy(56, arrow_y);
				printf("  ");
				if (arrow_y == 14)
					arrow_y = 10;
				else
					arrow_y += 2;
				break;
			}
			case 32:
			{
				if (arrow_y == 10)//계속하기
				{
					color(238);
					gotoxy(38, 6);
					printf("                          ");
					gotoxy(38, 7);
					printf("                          ");
					gotoxy(38, 8);
					printf("                          ");
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 10);
					printf("                          ");
					gotoxy(38, 12);
					printf("                          ");
					gotoxy(38, 14);
					printf("                          ");
					gotoxy(38, 16);
					printf("                          ");
					continue_num = 1;
					active();
				}
				else if (arrow_y == 12)//다시하기
				{
					color(238);
					gotoxy(38, 6);
					printf("                          ");
					gotoxy(38, 7);
					printf("                          ");
					gotoxy(38, 8);
					printf("                          ");
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 10);
					printf("                          ");
					gotoxy(38, 12);
					printf("                          ");
					gotoxy(38, 14);
					printf("                          ");
					gotoxy(38, 16);
					printf("                          ");
					for (int q = 0; q <= 15; q++)
					{
						screen[q] = 0;
					}
					continue_num = 0;
					active();
				}
				else if (arrow_y == 14)//게임종료
				{
					screen_type = 1;
					color(238);
					gotoxy(38, 6);
					printf("                          ");
					gotoxy(38, 7);
					printf("                          ");
					gotoxy(38, 8);
					printf("                          ");
					gotoxy(38, 9);
					printf("                          ");
					gotoxy(38, 10);
					printf("                          ");
					gotoxy(38, 12);
					printf("                          ");
					gotoxy(38, 14);
					printf("                          ");
					gotoxy(38, 16);
					printf("                          ");
					finish_screen();
				}
				break;
			}
			}
		}
		color(233);
		gotoxy(42, arrow_y);
		printf("▶");
		gotoxy(56, arrow_y);
		printf("◀");
	}
}

void information_screen()
{
	color(224);
	gotoxy(18, 7);
	printf("김건우 제작");
	Sleep(10);
	gotoxy(18, 10);
	printf("방향키 (");
	color(235);
	printf("↑,↓,←, →");
	color(224);
	printf("): ");
	color(233);
	printf("블럭이동");
	Sleep(10);
	gotoxy(18, 12);
	color(224);
	printf("ESC: ");
	color(233);
	printf("일시정지 & 종료");
	Sleep(10);
	gotoxy(18, 14);
	color(224);
	printf("SPACEBAR");
	color(233);
	printf(": 선택");
	color(233);
	gotoxy(14, 10);
	printf("ㆍ");
	gotoxy(14, 12);
	printf("ㆍ");
	gotoxy(14, 14);
	printf("ㆍ");

	gotoxy(8, 21);
	color(235);
	printf("이 게임은 2048의 규칙을 그대로 따릅니다.");
	gotoxy(8, 23);
	printf("오류를 찾았거나 추가하고 싶은 기능이 있다면");
	color(233);
	gotoxy(8, 24);
	printf("fantaandgom@gmail.com");
	color(235);
	printf("으로 메일 부탁드립니다.");

	color(224);
	gotoxy(16, 17);
	printf("메인화면으로 돌아가기");

	int arrow_y = 13;
	char ch_information;
	color(233);
	gotoxy(12, 17);
	printf("▶");
	gotoxy(39, 17);
	printf("◀");

	while (1)
	{
		if (_kbhit())
		{
			ch_information = _getch();
			if (ch_information == 32)
				main_screen();
		}
	}
}


void check_up()
{
	move_up(0);
	move_up(1);
	move_up(2);
	move_up(3);

	int re_a = 0;//당길 수 없는 경우가 12번 이상일 경우(=더이상 당길 수 있는 칸이 없는 경우) 카운트하기 위한 변수

	while (1)//더이상 당길 수 없을 때 까지 반복하기 위함
	{
		for (int i = 0; i <= 11; i++)
		{
			if (screen[i] == 0 && screen[i + 4] != 0)//칸이 비어있을 때 한칸 당기기
			{
				screen[i] = screen[i + 4];
				screen[i + 4] = 0;
				check_appear = 1;
			}
			else//당길 수 없는 경우가 있을 경우 re 카운트
			{
				re_a++;
			}
		}
		if (re_a >= 12)//더이상 당길 수 없는지 확인
		{
			break;
		}
		re_a = 0;
	}

	/*/
	for (int i = 0; i <= 3; i++)//세로줄 모두 같은 경우 (특수한 경우)
	{
		if (screen[i] == screen[i + 4] == screen[i + 8] == screen[i + 12])
		{
			screen[i] += screen[i];
			screen[i + 4] += screen[i];
			screen[i + 8] = 0;
			screen[i + 12] = 0;
		}
	}

	if (screen[4] == screen[8])//----- 2,3 행 먼저 확인 후 합쳐질 경우 다른 행에서 합칠 경우의 수 없음
	{
		screen[4] += screen[8];
		screen[8] = screen[12];
		width[0] = 1;
	}
	else if (screen[5] == screen[9])
	{
		screen[5] += screen[9];
		screen[9] = screen[13];
		width[1] = 1;
	}
	else if (screen[6] == screen[10])
	{
		screen[6] += screen[10];
		screen[10] = screen[14];
		width[2] = 1;
	}
	else if (screen[7] == screen[11])
	{
		screen[7] += screen[11];
		screen[11] = screen[15];
		width[3] = 1;
	}


	if (screen[8] == screen[12] && width[0] == 0)//-----  3~4행 확인 후 위로 당기기
	{
		screen[8] += screen[12];
		screen[12] = 0;
	}
	else if (screen[9] == screen[13] && width[1] == 0)
	{
		screen[9] += screen[13];
		screen[13] = 0;
	}
	else if (screen[10] == screen[14] && width[2] == 0)
	{
		screen[10] += screen[14];
		screen[14] = 0;
	}
	else if (screen[11] == screen[15] && width[3] == 0)
	{
		screen[11] += screen[15];
		screen[15] = 0;
	}


	if (screen[0] == screen[4] && width[0] == 0)//-----  1~2행 확인 후 위로 당기기
	{
		screen[0] += screen[4];
		screen[4] = screen[8];
		screen[8] = screen[12];
		screen[12] = 0;
	}
	else if (screen[1] == screen[5] && width[1] == 0)
	{
		screen[1] += screen[5];
		screen[5] = screen[9];
		screen[9] = screen[13];
		screen[13] = 0;
	}
	else if (screen[2] == screen[6] && width[2] == 0)
	{
		screen[2] += screen[6];
		screen[6] = screen[10];
		screen[10] = screen[14];
		screen[14] = 0;
	}
	else if (screen[3] == screen[7] && width[3] == 0)
	{
		screen[3] += screen[7];
		screen[7] = screen[11];
		screen[11] = screen[15];
		screen[15] = 0;
	}

	width[0] = 0;//각 줄 확인여부 초기화
	width[1] = 0;
	width[2] = 0;
	width[3] = 0;
	*/
	//실패작
}

void check_down()
{
	move_down(0);
	move_down(1);
	move_down(2);
	move_down(3);

	int re_b = 0;//당길 수 없는 경우가 12번 이상일 경우(=더이상 당길 수 있는 칸이 없는 경우) 카운트하기 위한 변수

	while (1)//더이상 당길 수 없을 때 까지 반복하기 위함
	{
		for (int i = 0; i <= 11; i++)
		{
			if (screen[i + 4] == 0 && screen[i] != 0)//칸이 비어있을 때 한칸 당기기
			{
				screen[i + 4] = screen[i];
				screen[i] = 0;
				check_appear = 1;
			}
			else//당길 수 없는 경우가 있을 경우 re 카운트
			{
				re_b++;
			}
		}
		if (re_b >= 12)//더이상 당길 수 없는지 확인
		{
			break;
		}
		re_b = 0;
	}
}

void check_left()
{
	move_left(0);
	move_left(4);
	move_left(8);
	move_left(12);

	int re_c = 0;//당길 수 없는 경우가 12번 이상일 경우(=더이상 당길 수 있는 칸이 없는 경우) 카우트하기 위한 변수

	while (1)//더이상 당길 수 없을 때 까지 반복하기 위함
	{
		for (int i = 0; i <= 15; i++)
		{
			if (i != 3 && i != 7 && i != 11 && i != 15)
			{
				if (screen[i] == 0 && screen[i + 1] != 0)//칸이 비어있을 때 한칸 당기기
				{
					screen[i] = screen[i + 1];
					screen[i + 1] = 0;
					check_appear = 1;
				}
				else//당길 수 없는 경우가 있을 경우 re 카운트
				{
					re_c++;
				}
			}
		}
		if (re_c >= 12)//더이상 당길 수 없는지 확인
		{
			break;
		}
		re_c = 0;
	}
}

void check_right()
{
	move_right(0);
	move_right(4);
	move_right(8);
	move_right(12);

	int re_d = 0;//당길 수 없는 경우가 12번 이상일 경우(=더이상 당길 수 있는 칸이 없는 경우) 카우트하기 위한 변수

	while (1)//더이상 당길 수 없을 때 까지 반복하기 위함
	{
		for (int i = 0; i <= 15; i++)
		{
			if (i != 3 && i != 7 && i != 11 && i != 15)
			{
				if (screen[i + 1] == 0 && screen[i] != 0)//칸이 비어있을 때 한칸 당기기
				{
					screen[i + 1] = screen[i];
					screen[i] = 0;
					check_appear = 1;
				}
				else//당길 수 없는 경우가 있을 경우 re 카운트
				{
					re_d++;
				}
			}
		}
		if (re_d >= 12)//더이상 당길 수 없는지 확인
		{
			break;
		}
		re_d = 0;
	}
}

//-------------------------------------------------------------------------중간 없을 때 합치는 경우 버그 수정하기--------
void move_up(int a)//위로 움직이기
{
	if (screen[a] == screen[a + 4] && screen[a] == screen[a + 8] && screen[a] == screen[a + 12])//전부 같을 경우
	{
		if (screen[a] != 0 && screen[a + 12])
		{
			screen[a] += screen[a + 4];
			screen[a + 4] = 0;
			screen[a + 8] += screen[a + 12];
			screen[a + 12] = 0;
			check_appear = 1;
		}
	}
	else if (screen[a] == screen[a + 4])//1~2행 같을경우
	{
		screen[a] += screen[a + 4];
		screen[a + 4] = 0;
		check_appear = 1;
		
		if (screen[a + 8] == screen[a + 12])//3~4행 같을경우
		{
			screen[a + 8] += screen[a + 12];
			screen[a + 12] = 0;
			check_appear = 1;
		}
	}
	else if (screen[a + 8] == screen[a + 12])//3~4행 같을경우
	{
		screen[a + 8] += screen[a + 12];
		screen[a + 12] = 0;
		check_appear = 1;
	}
	else if (screen[a + 4] == screen[a + 8])//2~3행 같을경우
	{
		screen[a + 4] += screen[a + 8];
		screen[a + 8] = 0;
		check_appear = 1;
	}
	else if (screen[a + 4] == 0 && screen[a + 8] == 0)//2,3행 비어있고 1,4행 같을경우
	{
		if (screen[a] == screen[a + 12])
		{
			screen[a] += screen[a + 12];
			screen[a + 12] = 0;
			check_appear = 1;
		}
	}
	else if (screen[a + 4] == 0 && screen[a] == screen[a + 8])//2행 비어있고 1,3행 같을경우
	{
		screen[a] += screen[a + 8];
		screen[a + 8] = 0;
		check_appear = 1;
	}
	else if (screen[a + 8] == 0 && screen[a + 4] == screen[a + 12])//3행 비어있고 2,4행 같을경우
	{
		screen[a + 4] += screen[a + 12];
		screen[a + 12] = 0;
		check_appear = 1;
	}
	else//움직이지 않았을 때
	{
		check_appear = 0;
	}
}

void move_down(int b)//아래로 움직이기
{
	if (screen[b] == screen[b + 4] && screen[b] == screen[b + 8] && screen[b] == screen[b + 12])//전부 같을 경우
	{
		if (screen[b] != 0 && screen[b + 12] != 0)
		{
			screen[b + 4] += screen[b];
			screen[b] = 0;
			screen[b + 12] += screen[b + 8];
			screen[b + 8] = 0;
			check_appear = 1;
		}
	}
	else if (screen[b + 8] == screen[b + 12])//3~4행 같을경우
	{
		screen[b + 12] += screen[b + 8];
		screen[b + 8] = 0;
		check_appear = 1;

		if (screen[b] == screen[b + 4])//1~2행 같을경우
		{
			screen[b + 4] += screen[b];
			screen[b] = 0;
			check_appear = 1;
		}
	}
	else if (screen[b] == screen[b + 4])//1~2행 같을경우
	{
		screen[b + 4] += screen[b];
		screen[b] = 0;
		check_appear = 1;
	}
	else if (screen[b + 4] == screen[b + 8])//2~3행 같을경우
	{
		screen[b + 8] += screen[b + 4];
		screen[b + 4] = 0;
		check_appear = 1;
	}
	else if (screen[b] == screen[b + 12])//2,3행 비어있고 1,4행 같을경우
	{
		if (screen[b + 4] == 0 && screen[b + 8] == 0)
		{
			screen[b + 12] += screen[b];
			screen[b] = 0;
			check_appear = 1;
		}
	}
	else if (screen[b + 4] == 0 && screen[b] == screen[b + 8])//2행 비어있고 1,3행 같을경우
	{
		screen[b + 8] += screen[b];
		screen[b] = 0;
		check_appear = 1;
	}
	else if (screen[b + 8] == 0 && screen[b + 4] == screen[b + 12])//3행 비어있고 2,4행 같을경우
	{
		screen[b + 12] += screen[b + 4];
		screen[b + 4] = 0;
		check_appear = 1;
	}
	else//움직이지 않았을 때
	{
		check_appear = 0;
	}
}

void move_left(int c)//왼쪽으로 움직이기
{
	if (screen[c] == screen[c + 1] && screen[c] == screen[c + 2] && screen[c] == screen[c + 3])//전부 같을 경우
	{
		if (screen[c] != 0 && screen[c + 3] != 0)
		{
			screen[c] += screen[c + 1];
			screen[c + 1] = 0;
			screen[c + 2] += screen[c + 3];
			screen[c + 3] = 0;
			check_appear = 1;
		}
	}
	else if (screen[c] == screen[c + 1])//1~2줄 같을경우
	{
		screen[c] += screen[c + 1];
		screen[c + 1] = 0;
		check_appear = 1;

		if (screen[c + 2] == screen[c + 3])//3~4줄 같을경우
		{
			screen[c + 2] += screen[c + 3];
			screen[c + 3] = 0;
			check_appear = 1;
		}
	}
	else if (screen[c + 2] == screen[c + 3])//3~4줄 같을경우
	{
		screen[c + 2] += screen[c + 3];
		screen[c + 3] = 0;
		check_appear = 1;
	}
	else if (screen[c + 1] == screen[c + 2])//2~3줄 같을경우
	{
		screen[c + 1] += screen[c + 2];
		screen[c + 2] = 0;
		check_appear = 1;
	}
	else if (screen[c + 1] == 0 && screen[c + 2] == 0)//2,3줄 비어있고 1,4줄 같을경우
	{
		if (screen[c] == screen[c + 3])
		{
			screen[c] += screen[c + 3];
			screen[c + 3] = 0;
			check_appear = 1;
		}
	}
	else if (screen[c + 1] == 0 && screen[c] == screen[c + 2])//2줄 비어있고 1,3줄 같을경우
	{
		screen[c] += screen[c + 2];
		screen[c + 2] = 0;
		check_appear = 1;
	}
	else if (screen[c + 2] == 0 && screen[c + 1] == screen[c + 3])//3줄 비어있고 2,4줄 같을경우
	{
		screen[c + 1] += screen[c + 3];
		screen[c + 3] = 0;
		check_appear = 1;
	}
	else//움직이지 않았을 때
	{
		check_appear = 0;
	}
}

void move_right(int d)//오른쪽으로 움직이기
{
	if (screen[d] == screen[d + 1] && screen[d] == screen[d + 2] && screen[d] == screen[d + 3])//전부 같을 경우
	{
		if (screen[d] != 0 && screen[d + 3] != 0)
		{
			screen[d + 1] += screen[d];
			screen[d] = 0;
			screen[d + 3] += screen[d + 2];
			screen[d + 2] = 0;
			check_appear = 1;
		}
	}
	if (screen[d + 2] == screen[d + 3])//3~4줄 같을경우
	{
		screen[d + 3] += screen[d + 2];
		screen[d + 2] = 0;
		check_appear = 1;
	}
	else if (screen[d] == screen[d + 1])//1~2줄 같을경우
	{
		screen[d + 1] += screen[d];
		screen[d] = 0;
		check_appear = 1;

		if (screen[d + 2] == screen[d + 3])//3~4줄 같을경우
		{
			screen[d + 3] += screen[d + 2];
			screen[d + 2] = 0;
			check_appear = 1;
		}
	}
	else if (screen[d + 1] == screen[d + 2])//2~3줄 같을경우
	{
		screen[d + 2] += screen[d + 1];
		screen[d + 1] = 0;
		check_appear = 1;
	}
	else if (screen[d + 1] == 0 && screen[d + 2] == 0)//2,3줄 비어있고 1,4줄 같을경우
	{
		if (screen[d] == screen[d + 3])
		{
			screen[d + 3] += screen[d];
			screen[d] = 0;
			check_appear = 1;
		}
	}
	else if (screen[d + 1] == 0 && screen[d] == screen[d + 2])//2줄 비어있고 1,3줄 같을경우
	{
		screen[d + 2] += screen[d];
		screen[d] = 0;
		check_appear = 1;
	}
	else if (screen[d + 2] == 0 && screen[d + 1] == screen[d + 3])//3줄 비어있고 2,4줄 같을경우
	{
		screen[d + 3] += screen[d + 1];
		screen[d + 1] = 0;
		check_appear = 1;
	}
	else//움직이지 않았을 때
	{
	check_appear = 0;
	}
}

int check_end()
{
	for (int i = 0; i <= 15; i++)
	{
		if (screen[i] == 0)
			return 1;
	}

	for (int a = 0; a <= 14; a++)
	{
		if (a != 3 && a != 7 && a != 11)
		{
			if (screen[a] == screen[a + 1])
				return 1;
		}
	}

	for (int b = 0; b <= 11; b++)
	{
		if (screen[b] == screen[b + 4])
			return 1;
	}

	return 0;

	/*
	int end_count = 0;//합칠 수 없는 경우 카운트
	for (int i = 0; i <= 14; i++)
	{
		if (screen[i] != screen[i + 1] && i != 3 && i != 7 && i != 11)
			end_count++;
		else if (screen[i] == screen[i + 1])
			return 1;//진행할 수 있음
	}
	
	for (int l = 0; l <= 11; l++)
	{
		if (screen[l] != screen[l + 4])
			end_count++;
		else if (screen[l] == screen[l + 4])
			return 1;//진행할 수 있음
	}

	if (end_count >= 24)
		return 0;
		*/
}



int main()
{
	CursorView(0);
	print_start_screen();
	main_screen();
}




void screen_test()
{
	gotoxy(1, 1);
	printf("%d	%d	%d	%d\n", screen[0], screen[1], screen[2], screen[3]);
	printf("%d	%d	%d	%d\n", screen[4], screen[5], screen[6], screen[7]);
	printf("%d	%d	%d	%d\n", screen[8], screen[9], screen[10], screen[11]);
	printf("%d	%d	%d	%d\n", screen[12], screen[13], screen[14], screen[15]);
}

void check_bug()
{
	int bug = 1;//버그 있을 경우 1, 없을경우 0
	color(232);

	for (int n = 0; n <= 15; n++)
	{
		bug = 1;
		switch (screen[n])
		{
		case 0:
			bug = 0; break;
		case 2:
			bug = 0; break;
		case 4:
			bug = 0; break;
		case 8:
			bug = 0; break;
		case 16:
			bug = 0; break;
		case 32:
			bug = 0; break;
		case 64:
			bug = 0; break;
		case 128:
			bug = 0; break;
		case 256:
			bug = 0; break;
		case 512:
			bug = 0; break;
		case 1024:
			bug = 0; break;
		case 2048:
			bug = 0; break;
		}

		if (bug == 1)
		{
			gotoxy(50, 6 + n);
			printf("screen[%d] 오류", n);
		}
		else if (bug == 0)
		{
			gotoxy(50, 6 + n);
			printf("screen[%d} 정상", n);
		}
	}
}
