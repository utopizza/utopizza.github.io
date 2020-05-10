# 挑战杯河北省大学生课外学术科技作品竞赛



一、简介

参赛作品是《基于四旋翼无人机的输电线路智能巡检系统》，用无人机代替人工对高压电线塔进行智能巡检。其智能体现于：

1. 根据高压电线塔的地理坐标自动计算出高压线塔检测点的位置
2. 根据两塔之间的距离和气温、风速、覆冰等气象条件计算两塔之间高压电线的应力弧垂
3. 自动制定飞行计划，实现无人控制，自动巡检

无人机硬件：

![](/2015-05-28-项目-挑战杯河北省大学生课外学术科技作品竞赛/无人机硬件.jpg)

其中我负责的部分是无人机飞控系统的开发：

![](/2015-05-28-项目-挑战杯河北省大学生课外学术科技作品竞赛/无人机飞控系统.jpg)

二、飞控系统的开发

该系统基于一个开源的飞控地面站系统——MissionPlanner进行改造。我所做的工作有：

1. 清理多余的面板和插件，只留下我们需要的功能，使软件更加简洁好用。
2. 加入一些新的小功能，如从文件读入高压输电塔的经纬度、在地图上选取高压输电塔的坐标点并且将其转换成经纬度显示在面板上等等。
3. 加入自动生成巡检点的算法。该算法根据用户在地图上选取的点，或者从文件中读入的高压输电塔的经纬度，自动计算出关键巡检点，并生成飞行计划。

![](/2015-05-28-项目-挑战杯河北省大学生课外学术科技作品竞赛/关键巡检点.jpg)

该软件是基于WinForm实现的，因此前面两点都不难实现，只是引用的插件和使用的控件比较多，理清各个层次的引用关系略复杂。此处不再赘述。

现在来分析第3点的算法。在用户添加一个航点(高压输电塔)的时候，我们能获取的数据只有塔的经度、纬度、海拔，那怎么计算出它周围对应的12个航点呢？这个问题的关键是要求出两个塔之间的电线的走向。我的思路是，地球的经纬度相当于地面的坐标轴，考虑到我们的监测范围是一个面积很小的范围，而且电线塔与电线塔之间的距离也不大，我们可以把地图的经纬度近似作为一个直角坐标系，经度作为X轴，纬度作为Y轴，于是地图上的每个航点就抽象成了一个包含坐标的点。

![](/2015-05-28-项目-挑战杯河北省大学生课外学术科技作品竞赛/坐标.jpg)

有了这个坐标，我们就可以把本航点和上一个航点连起来，而这条线段就是两个电线塔之间的电线。因为有了坐标轴，于是现在我们可以把电线塔的“走向”进行量化——斜率。这样，每两个塔之间的电线走向就可以轻易得到：

$$k=\frac{y_0-y_1}{x_0-x_1}$$

有了斜率之后，我们就可以给每个塔的周围的对应的12个监测航点进行定位了，如图

![](/2015-05-28-项目-挑战杯河北省大学生课外学术科技作品竞赛/坐标2.jpg)

(说明：图中的 $(x',y')$ 是前一个航点，$(x_0,y_0)$ 是要计算的本行航点。图中下方的1、2、3、4分别就是四个位置的监测航点的坐标位置了。而每个位置均有3个监测航点，只是高度不同而已，可以通过代码进行设置)

核心代码:

```
//添加一个塔，自动计算出12个航点
private void AddTower_Click(object sender, EventArgs e)
{
    double towerlat = Convert.ToDouble(TXT_towerlat.Text.ToString().Trim());
    double towerlong = Convert.ToDouble(TXT_towerlng.Text.ToString().Trim());

    //先往地图中加入塔点
    AddWPToMap(towerlat, towerlong, 0);

    //根据塔点自动生成的航点

    //上一个塔点
    PointLatLngAlt wp_last = pointlist[pointlist.Count - 2];

    //该塔点
    PointLatLngAlt wp_this = pointlist[pointlist.Count - 1];

    //通过两点的斜率k求夹角a(PI)
    double k = (wp_last.Lat - wp_this.Lat) / (wp_last.Lng - wp_this.Lng);
    double a = Math.Atan(k);

    //监测半径r
    double r = 0.0001;

    //添加4*4=16个监视航点

    //第一角度4个航点
    double Lat1 = (float)(wp_this.Lat + r * Math.Cos(0.25 * Math.PI - a));
    double Lng1 = (float)(wp_this.Lng - r * Math.Sin(0.25 * Math.PI - a));
    int Alt1 = 100;
    AddWPToMap(Lat1, Lng1, Alt1);
    AddWPToMap(Lat1, Lng1, Alt1 - 5);
    AddWPToMap(Lat1, Lng1, Alt1 - 8);
    AddWPToMap(Lat1, Lng1, Alt1);

    //第二角度4个航点
    double Lat2 = (float)(wp_this.Lat + r * Math.Sin(0.25 * Math.PI - a));
    double Lng2 = (float)(wp_this.Lng + r * Math.Cos(0.25 * Math.PI - a));
    int Alt2 = 100;
    AddWPToMap(Lat2, Lng2, Alt2);
    AddWPToMap(Lat2, Lng2, Alt2 - 5);
    AddWPToMap(Lat2, Lng2, Alt2 - 8);
    AddWPToMap(Lat2, Lng2, Alt2);

    //第三角度4个航点
    double Lat3 = (float)(wp_this.Lat - r * Math.Cos(0.25 * Math.PI - a));
    double Lng3 = (float)(wp_this.Lng + r * Math.Sin(0.25 * Math.PI - a));
    int Alt3 = 100;
    AddWPToMap(Lat3, Lng3, Alt3);
    AddWPToMap(Lat3, Lng3, Alt3 - 5);
    AddWPToMap(Lat3, Lng3, Alt3 - 8);
    AddWPToMap(Lat3, Lng3, Alt3);

    //第四角度3个航点
    double Lat4 = (float)(wp_this.Lat - r * Math.Sin(0.25 * Math.PI - a));
    double Lng4 = (float)(wp_this.Lng - r * Math.Cos(0.25 * Math.PI - a));
    int Alt4 = 100;
    AddWPToMap(Lat4, Lng4, Alt4);
    AddWPToMap(Lat4, Lng4, Alt4 - 5);
    AddWPToMap(Lat4, Lng4, Alt4 - 8);
    AddWPToMap(Lat4, Lng4, Alt4);

    //再次加入该塔点，让飞机飞回塔顶
    AddWPToMap(towerlat, towerlong, 0);

}
```

