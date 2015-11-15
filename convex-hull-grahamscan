#include <iostream>
#include <vector>
#include <ctime>
#include <cstdlib>
#include <cmath>
#include <algorithm>
#include <fstream>
using namespace std;
const double PI = 3.1415;
struct Point {
	int x, y;
	Point(int x, int y) :x(x), y(y){}
};
struct MarkPoint{
	Point pt;
	bool mark;//表示是否删掉了
	double angle;//表示极角
	double dist;//表示该点到 极点的距离
	MarkPoint(Point pt, bool mark, double angle, double dist ) :pt(pt), mark(mark), angle(angle),dist(dist){}
};
//生成随机点
void gen_rand_pts(vector<MarkPoint> &mpts,int low, int high, int n)
{
	srand(time(NULL));
	for (int i = 0; i != n; ++i)
	{
		MarkPoint mp(Point(low + rand() % (high - low + 1), low + rand() % (high - low + 1)), true, 0.0, 0.0);
		vector<MarkPoint>::iterator beg = mpts.begin(), end = mpts.end();
		while (beg != end)
		{
			if (beg->pt.x == mp.pt.x&& beg->pt.y == mp.pt.y)
			{
				break;
			}
			++beg;
		}
		if (beg != end)
		{
			--i;
			continue;
		}
		mpts.push_back(mp);
	}
}
//极角比较函数
bool cmp(MarkPoint mp1, MarkPoint mp2)
{
	return mp1.angle < mp2.angle;
}
//计算极角
void calc_angle(vector<MarkPoint> &mpts, vector<Point> &pts)
{
	//找到最下面的点
	vector<MarkPoint>::iterator beg = mpts.begin(), end = mpts.end(), miny_ptr=beg;
	int miny = beg->pt.y;
	++beg;
	while (beg!=end)
	{
		if (beg->pt.y < miny)
		{
			miny = beg->pt.y;
			miny_ptr = beg;
		}
		++beg;
	}
	beg = mpts.begin();
	//将最下面的点放在第一位
	swap<MarkPoint>(*beg, *miny_ptr);
	//根据上面找到的点 分别计算每个点的极角 和距离
	beg = mpts.begin();
	miny_ptr = beg;
	++beg;
	end = mpts.end();
	while (beg!=end)
	{
		if (beg->pt.x == miny_ptr->pt.x)
		{
			beg->angle = PI / 2;
		}
		else{
			//atan 返回弧度
			double angle = atan((double)(beg->pt.y - miny_ptr->pt.y) / (beg->pt.x - miny_ptr->pt.x));
			beg->angle = angle > 0 ? angle : angle+PI;
		}
		beg->dist = sqrt((beg->pt.x - miny_ptr->pt.x)*(beg->pt.x - miny_ptr->pt.x) + (beg->pt.y - miny_ptr->pt.y)*(beg->pt.y - miny_ptr->pt.y));
		++beg;
	}
	//对上面找到的极角排序 
	sort(++mpts.begin(), mpts.end(), cmp);
	//删除极角相同 距离极点较近的点
	beg = ++mpts.begin();
	end = mpts.end();
	if (beg != end)
	{
		vector<MarkPoint>::iterator last_far = beg;
		++beg;
		while (beg != end)
		{
			if (fabs(beg->angle - last_far->angle) < 1e-5)//极角一样
			{
				beg->dist = sqrt((beg->pt.x - miny_ptr->pt.x)*(beg->pt.x - miny_ptr->pt.x) + (beg->pt.y - miny_ptr->pt.y)*(beg->pt.y - miny_ptr->pt.y));
				if (last_far->dist < beg->dist)//当前点更远
				{
					last_far->mark = false;
					last_far = beg;
				}
				else
				{
					beg->mark = false;
				}
			}
			else
			{
				last_far = beg;
			}
			++beg;
		}
	}
	//将得到的结果 输入pts
	beg = mpts.begin();
	end = mpts.end();
	while (beg != end)
	{
		if (beg->mark == true)
		{
			pts.push_back(beg->pt);
		}
		++beg;
	}
}
void scan(const vector<Point> &pts, vector<Point> &res)
{
	if (pts.size() < 3)
	{
		return;
	}
	vector<Point>::const_iterator beg = pts.begin(), end = pts.end();
	for (int i = 0; i != 3; ++i)
	{
		res.push_back(*beg);
		++beg;
	}
	Point p0 = res[0];
	while (beg != end)
	{
		bool con = true;
		while (con)
		{
			Point top = res[res.size() - 1];
			Point next_top = res[res.size() - 2];
			if (
				((top.y - next_top.y)*(beg->x - next_top.x) - (top.x - next_top.x)*(beg->y - next_top.y))*
				((p0.y - next_top.y)*(beg->x - next_top.x) - (p0.x - next_top.x)*(beg->y - next_top.y)) >= 0
				)//栈顶点 要删掉
			{
				res.pop_back();
			}
			else
			{
				con = false;
			}
		}
		res.push_back(*beg);
		++beg;
	}
}
void output_pts(vector<Point> &pts, string file_path)
{
	ofstream out(file_path);
	if (out.is_open())
	{
		vector<Point>::iterator beg = pts.begin(), end = pts.end();
		while (beg != end)
		{
			out << beg->x << " " << beg->y << endl;
			++beg;
		}
	}
	out.close();
}
void graham_scan(int low,int high,int n)//生成[low,low]-[high,high]之间的n个点
{
	vector<MarkPoint> mpts;
	//生成随机点
	gen_rand_pts(mpts, low, high, n);
	//计算极角，并对极角排序
	vector<Point> pts;
	calc_angle(mpts, pts);
	//依次对上步结果集 进行考察 用栈实现
	vector<Point> chpts;
	scan(pts, chpts);
	//输出栈中的 点即可
	output_pts(chpts, "convex-hull-grahamscan.txt");
}
int main()
{
	graham_scan(0,3,5);
	return 0;
}
