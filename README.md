
#include<algorithm>
#include<string.h>
#include<stdlib.h>
#include<stdio.h>
#include<time.h>
using namespace std;
const int maxsize=1000;
//日期
struct  date
{
    int y;
    int m;
    int d;
    int h;
    int mi;
    int s;
};
//定义商品数据，包括商品编号，商品名称，商品进价，出价
struct goods_data
{
    char node[20];
    char name[20];
    float cprice;
    float sprice;
    int y;
    int m;
    int d;
    int cm;
};
//商品结点
struct goods
{
   struct goods_data data;
   struct goods *next;
};
//定义会员信息数据包括会员卡号、会员姓名、会员联系方式、会员积分、会员卡创建日期、有效日期、结束日期
struct VIP_data
{
    char  code[20];
    char name[20];
    char tel[20];
    int score;
    int l;
    date start;
    date eend;
};
//会员结点
struct VIP
{
    VIP_data data;
    VIP *next;
};
//定义商品交易表中，商品表，包括商品编号，名称，数量，售价
struct deal_goods
{
    char node[20];
    char name[20];
    int num;
    float in_price;
    float price;
};
//定义商品交易表，包括交易号，交易日期，收银员号，机号，商品编号，商品名称，数量，售价，收银，应付金额，实付金额，优惠金额，找零，付款方式
struct deal
{
    char deal_node[20];          //交易号
    date d;                      //日期
    char s_node[20];             //收银员号
    char m_node[20];             //机号
    deal_goods *elem;            //商品表
    int l;                       //商品总个数
    float pprice;                //总金额
    int if_vip;                  //是否vip,1:是，0：不是
    int if_act;                  //是否活动期间
    int if_discount;             //是否折扣商品
    float s_price;               //应付金额，优惠之后
    int pay_way ;                //付款方式 0:现金 1:支付宝 2:微信 3：银行卡 4：代金券
    float d_price;               //优惠金额
    float g_money;               //实收金额
    float p_money;               //找零
};
//创建一个销售表,销售商品名称，商品进价，销售商品价格，销售商品总量，销售额，利润
struct sale_data
{
    char name[20];
    float in_price;
    float price;
    int sale_num;
    float sale_money;
    float profit;
};
struct sale_list
{
    sale_data *elem;
    int length;
    int listsize;
};
//定义员工信息，包括员工编号，姓名，联系方式，家庭住址，职位，工资，身份证号，入店时间，年龄
struct staff_data
{
    char node[20];
    char name[20];
    char tel[20];
    char adress[50];
    char position[20];
    int salary;
    char ID_number[20];
    date in_time;
    int old;
};
//定义供货商数据
struct supplier_data
{
    char node[20];
    char name[20];
    char goods_name[20];
    char tel[20];
    char adress[20];
};
//结点
struct supplier
{
    supplier_data data;
    supplier *next;
};
//结点
struct staff
{
    staff_data data;
    staff *next;
};
//定义进货表数据,包括商品信息，进货时间，供货商
struct supply_data
{
    char node[20];
    char name[20];
    float cprice;
    float sprice;
    int y;
    int m;
    int d;
    int cm;
    char sname[20];
};
struct supply
{
    supply_data data;
    supply *next;
};
//收银台，包括收银台编号，对应收银员编号，起始收银台金额，最终收银台金额
struct counter
{
    char cnode;
    char snode;
    int s;
    int e;
};
VIP *vhead;
goods *ghead;
staff *shead;
supplier *suhead;
supply *slhead;
int count_good;
int count_VIP;
FILE *p1;
FILE *p2;
FILE *fp;
FILE *fg;
FILE *fs;
FILE *fbg;
FILE *fsu;
FILE *fsl;
FILE *fpe;
FILE *fge;
FILE *fse;
FILE *fbge;
FILE *fsue;
FILE *fsle;
//获取现在时间
date get_date()
{
    date dt;
     time_t timp;
     struct tm *p;
     time(&timp);
     p=gmtime(&timp);
     dt.y=p->tm_year+1900;
     dt.m=p->tm_mon+1;
     dt.d=p->tm_mday;
     dt.h=p->tm_hour+8;
     dt.mi=p->tm_min;
     dt.s=p->tm_sec;
     return dt;
}
//建立商品的头结点
void Init_head_goods(goods *&g1)
{
    g1=(goods *)malloc(sizeof(goods));
    g1->next=NULL;
}
//商品线性表的初始化、将商品库存储到线性表中
void Init_goods()
{
    if((fg=fopen("Goods catlog.txt","r+"))==NULL)
    {
        printf("can not open Goods Catlog");
        exit(0);
    }
    goods *gp,*gpr;
    Init_head_goods(ghead);
    gpr=ghead;
    count_good=0;
    while(1)
    {
        gp=(goods *)malloc(sizeof(goods));
       if(gp==NULL){
        printf("no enough memory!");
        exit(0);
       }
       if(fscanf(fg,"%s%s%f%f%d%d%d%d",gp->data.node,gp->data.name,&gp->data.cprice,&gp->data.sprice,&gp->data.y,&gp->data.m,&gp->data.d,&gp->data.cm)!=EOF){
        gpr->next=gp;
        gpr=gpr->next;
       }
       else
        break;
        count_good++;
    }
}
//输出商品表
void show_goods()
{
    goods *gq=ghead->next;
    printf("编号 名称   数量  进价  卖价  生产日期  保质期\n");
    while(gq->next!=NULL){
        printf("%10s %-15s %-7.2f %-7.2f%-4d-%d-%d %-4d\n",gq->data.node,gq->data.name,gq->data.cprice,gq->data.sprice,gq->data.y,gq->data.m,gq->data.d,gq->data.cm);
        gq=gq->next;
    }
}
//找到商品s，用于购物小票
goods *find_goods(goods *&head,char s[])
{
    goods *g=head->next;
    goods *rg=head;
    goods *p;
    int flag=0;
    while(g->next!=NULL)
    {
        if(strcmp(s,g->data.node)==0){
            flag=1;
            p=g;
            rg->next=g->next;
            delete g;
            return p;
        }
        else{
            g=g->next;
            rg=rg->next;
        }
    }
    if(!flag){
        printf("there is no such goods");
        exit(0);
    }
    return p;
}
//将更新商品信息写入文件
void output_goods(goods *head)
{
    if((fge=fopen("after_goods.txt","w+"))==NULL)
    {
        printf("can not build after_goods.txt\n");
        exit(0);
    }
    goods *gp=head->next;
    while(gp->next!=NULL){
       fprintf(fge,"%-20s%-20s%-7.2f%-7.2f%6d%-6d%-6d%-6d\n",gp->data.node,gp->data.name,gp->data.cprice,gp->data.sprice,gp->data.y,gp->data.m,gp->data.d,gp->data.cm);
       gp=gp->next;
    }
    fclose(fge);
}
//销毁商品链表
void delete_goods(goods *&head)
{
    goods *p=head,*q;
    if(head==NULL)
        return ;
    while(p!=NULL){
        q=p->next;
        free(p);
        p=q;
    }
    free(head);
}
//建立会员线性表头结点
void Init_head_VIP(VIP *&p)
{
    p=(VIP *)malloc(sizeof(VIP));
    p->next=NULL;
}
//初始化会员线性表、将会员信息存储到会员线性表中
void Init_VIP()
{
    if((fg=fopen("Goods catlog.txt","r+"))==NULL)
    {
        printf("can not open Goods Catlog");
        exit(0);
    }
    VIP *gp,*gpr;
    Init_head_VIP(vhead);
    gpr=vhead;
      if((fp=(fopen("sVIP catlog.txt","r+")))==NULL)
    {
        printf("can not open sVIP catlog.txt");
        exit(0);
    }
    VIP *v1,*v2;
    Init_head_VIP(vhead);
    v2=vhead;
    count_VIP=0;
    while(1)
    {
        v1=(VIP *)malloc(sizeof(VIP));
        if(v1==NULL)
        {
            printf("no enough memory!\n");
            exit(0);
        }
        if(fscanf(fp,"%s%s%s%d%d%d%d%d",v1->data.code,v1->data.name,v1->data.tel,&v1->data.score,&v1->data.start.y,&v1->data.start.m,&v1->data.start.d,&v1->data.l)!=EOF)
        {
            v2->next=v1;
            v2=v2->next;
        }
        else
            break;
    }
}
void show_vip()
{
    VIP *v3=vhead->next;
     while(v3->next!=NULL){
        printf("%s %s %s %d %d %d %d %d\n",v3->data.code,v3->data.name,v3->data.tel,v3->data.score,v3->data.start.y,v3->data.start.m,v3->data.start.d,v3->data.l);
        v3=v3->next;
    }
}
//找到会员s,并返回
VIP *find_vip(VIP *head,char code[])
{
    VIP *p=head->next;
    int flag=0;
    while(p->next!=NULL)
    {
        if(strcmp(code,p->data.code)==0)
        {
            flag=1;
            return p;
        }
        p=p->next;
    }
    if(!flag){
        printf("no such vip!\n");
        exit(0);
    }
}
//判断会员卡是否过期
int if_overdue(VIP *v)
{
    int y=v->data.start.y;
    int m=v->data.start.m;
    int d=v->data.start.m;
    date td=get_date();
    int j=(td.y-2000)*365+td.m*30+td.d-(y-2000)*365+td.m*30+td.d;
    if(j>v->data.l*30)
        return 1;
    return 0;
}
//添加一个新的会员
void add_vip(char code[],char name[],char tel[],int l,int score)
{
    date d1;
    d1=get_date();
    VIP *v1,*p;
    v1=(VIP *)malloc(sizeof(VIP));
    strcpy(v1->data.code,code);
    strcpy(v1->data.name,name);
    strcpy(v1->data.tel,tel);
    v1->data.l=l;
    v1->data.score=score;
    v1->data.start=d1;
    p-vhead->next;
    v1->next=p->next;
    p->next=v1;
}
//将更新会员信息写入文件
void output_vip(VIP *head)
{
    VIP *v1=head->next;
    if((fpe=fopen("after_vip.txt","w+"))==NULL)
    {
        printf("can not build after_vip.txt\n");
        exit(0);
    }
    while(v1->next!=NULL){
        fprintf(fpe,"%-20s%-20s%-20s%-6d%-6d%-6d%-6d%-6d\n",v1->data.code,v1->data.name,v1->data.tel,v1->data.score,v1->data.start.y,v1->data.start.m,v1->data.start.d,v1->data.l);
        v1=v1->next;
    }
    fclose(fpe);
}
//销毁会员线性表
void delete_VIP(VIP *&head)
{
    VIP *p=head,*q;
    while(p!=NULL){
        q=p->next;
        free(p);
        p=q;
    }
    free(head);
}
//初始化商品小票
void Init_deal(deal &L)
{
    L.elem=new deal_goods[maxsize];
    L.l=0;
}
//找到商品小票中商品编号，并在其数量中加一
int find_deal_goods(deal &L,char s[])
{
    if(L.l==0)
        return 0;
    for(int i=1;i<=L.l;i++)
        if(strcmp(s,L.elem[i].name)==0)
    {
        L.elem[i].num++;
        return 1;
    }
    return 0;
}
//销毁商品小票
void delete_deal(deal &L)
{
    if(L.elem!=NULL)
        delete []L.elem;
}
//初始化销售表
void Init_sale(sale_list &s,int listsize=maxsize)
{
    s.elem=new sale_data[listsize];
    s.length=0;
    s.listsize=listsize;
}
//如果销售表中已存在此商品，数量加一,并返回1
int find_sale_goods(sale_list &s,char name[],float t,int num)
{
    if(s.length==0)
        return 0;
    for(int i=1;i<=s.length;i++){
        if(strcmp(s.elem[i].name,name)==0){
            s.elem[i].sale_money+=s.elem[i].price*t*num;
            s.elem[i].sale_num+=num;
            s.elem[i].profit+=num*(t*s.elem[i].price-s.elem[i].in_price);
            return 1;
        }
    }
    return 0;
}
//用商品交易表来建立销售表
void build_sale(sale_list &s,deal d)
{
    int L=d.l;
    float t=1.0;
    if(d.if_vip) t=t*0.99;
    if(d.if_act) t=t*0.99;
    for(int i=1;i<=d.l;i++){
        if(!find_sale_goods(s,d.elem[i].name,t,d.elem[i].num)){
                int l=++s.length;
            strcpy(s.elem[l].name,d.elem[i].name);
            s.elem[l].in_price=d.elem[i].in_price;
            s.elem[l].sale_num=d.elem[i].num;
            s.elem[l].price=d.elem[i].price;
            s.elem[l].sale_money=t*d.elem[i].num*d.elem[i].price;
            s.elem[l].profit=s.elem[l].sale_money-s.elem[l].sale_num*s.elem[l].in_price;
           // s.elem[l].profit=s.elem[l].sale_num*(s.elem[l].sale_money-s.elem[i].in_price)*t;
        }
    }
}
//寻找顺序表中元素
int find_elem(sale_list &s,char name[],int n,float t)
{
    for(int i=0;i<s.length;i++){
        if(strcpy(name,s.elem[i].name)==0)
            s.elem[i].sale_num+=n;
            s.elem[i].sale_money+=t;
            return 1;
    }
    return 0;
}
//输出销售表中元素
void show_sale(sale_list sl)
{
    for(int i=1;i<=sl.length;i++)
             printf("%-20s %-7.2f %-7.2f %-5d %-7.2f %-7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);

}
//按照销售额排名
void rank1(sale_list &s)
{
    for(int i=1;i<=s.length;i++){
       for(int j=i+1;j<=s.length;j++){
        if(s.elem[i].sale_money<s.elem[j].sale_money){
            sale_data t;
            t=s.elem[i];
            s.elem[i]=s.elem[j];
            s.elem[j]=t;
        }
       }
    }
}
//按照销售量排名
void rank2(sale_list &s)
{
    for(int i=1;i<=s.length;i++){
        for(int j=i+1;j<=s.length;j++){
            if(s.elem[i].sale_num<s.elem[j].sale_num){
                sale_data t;
                t=s.elem[i];
                s.elem[i]=s.elem[j];
                s.elem[j]=t;
            }
        }
    }
}
//按照销售利润排名
void rank3(sale_list &s)
{
    for(int i=1;i<=s.length;i++){
        for(int j=i+1;j<=s.length;j++){
            if(s.elem[i].profit<s.elem[j].profit){
                sale_data t;
                t=s.elem[i];
                s.elem[i]=s.elem[j];
                s.elem[j]=t;
            }
        }
    }
}
//销毁线性表
void Delete_sale(sale_list &s)
{
    delete []s.elem;
    s.length=0;
}
//员工的初始化
void Init_staff()
{
    shead=(staff *)malloc(sizeof(staff));
    if(shead==NULL)
    {
        printf("there is no enough memory for staff!\n");
        exit(0);
    }
    shead->next=NULL;
    if((fs=(fopen("staff.txt","r+")))==NULL){
        printf("can not find staff.txt!");
        exit(0);
    }
    staff *sp,*spr;
    spr=shead;
    while(1)
    {
        sp=(staff *)malloc(sizeof(staff));
        if(sp==NULL)
        {
            printf("no enough memory!\n");
            exit(0);
        }
        if(fscanf(fs,"%s%s%s%s%s%d%s%d%d%d%d",sp->data.node,sp->data.name,sp->data.tel,sp->data.adress,sp->data.position,&sp->data.salary,sp->data.ID_number,&sp->data.in_time.y,&sp->data.in_time.m,&sp->data.in_time.d,&sp->data.old)!=EOF)
        {
            spr->next=sp;
            spr=spr->next;
        }
        else
            break;
    }
}
void show_staff(staff *s)
{
    staff *p=s->next;
     while(p->next!=NULL)
    {
        printf("%s %s %s %s %s %d %s %d %d %d %d\n",p->data.node,p->data.name,p->data.tel,p->data.adress,p->data.position,p->data.salary,p->data.ID_number,p->data.in_time.y,p->data.in_time.m,p->data.in_time.d,p->data.old);
        p=p->next;
    }
}
void delete_staff(staff *&head)
{
    staff *sp,*spr;
    sp=head;
    while(sp->next!=NULL){
        spr=sp->next;
        free(sp);
        sp=spr;
    }
    free(head);
}
//检查商品库中是否有过期产品，如果有，记录下来，并从库中删除
void check_goods()
{
    if((fbg=fopen("overdue_goods.txt","w+"))==NULL)
    {
        printf("can not build overdue_goods.txt\n");
        exit(0);
    }
    fprintf(fbg,"%s\n","过期商品");
    int y,m,d,c;
    int ty,tm,td;
    date t=get_date();
    ty=t.y;
    tm=t.m;
    td=t.d;
    goods *g=ghead->next;
    goods *g1=ghead;
    while(g->next!=NULL)
    {
        y=g->data.y;
        m=g->data.m;
        d=g->data.d;
        c=g->data.cm;
        int j=(ty-2000)*365+tm*30+td-(y-2000)*365-m*30-d;
        if(j>g->data.cm*30 && c!=0){
            fprintf(fbg,"%s %s %d %d %d %d\n",g->data.node,g->data.name,g->data.y,g->data.m,g->data.d,g->data.cm);
            g1->next=g->next;
            delete g;
            g=g1->next;
        }
        else{
            g=g->next;
            g1=g1->next;
        }
    }
    fclose(fbg);

}
void Init_supplier()
{
    suhead=(supplier *)malloc(sizeof(supplier));
    if(suhead==NULL){
        printf("There is no enough memory!\n");
        exit(0);
    }
    suhead->next=NULL;
    if((fsu=fopen("supplier.txt","r+"))==NULL){
        printf("can not open the supplier.txt\n");
        exit(0);
    }
    supplier *s1,*s2;
    s2=suhead;
    while(1)
    {
        s1=(supplier *)malloc(sizeof(supplier));
        if(fscanf(fsu,"%s%s%s%s%s",s1->data.node,s1->data.name,s1->data.goods_name,s1->data.tel,s1->data.adress)!=EOF){
            s2->next=s1;
            s2=s2->next;
        }
        else
            break;
    }
}
void show_supplier()
{
    supplier *s1=suhead->next;
    while(s1->next!=NULL){
        printf("%s %s %s %s %s \n",s1->data.node,s1->data.name,s1->data.goods_name,s1->data.tel,s1->data.adress);
        s1=s1->next;
    }
}
void delete_supplier(supplier *suhead)
{
    supplier *s1,*s2;
    s1=suhead->next;
    while(s1->next!=NULL){
        s2=s1->next;
        free(s1);
        s1=s2;
    }
    free(suhead);
}
void Init_supply()
{
    slhead=(supply *)malloc(sizeof(supply));
    if(slhead==NULL){
        printf("no enough memory!\n");
        exit(0);
    }
    slhead->next=NULL;
    if((fsl=fopen("supply.txt","r+"))==NULL){
        printf("can not open supply.txt\n");
        exit(0);
    }
    supply *s1,*s2;
    s2=slhead;
    while(1)
    {
        s1=(supply*)malloc(sizeof(supply));
        if(s1==NULL){
            printf("no enough memory!\n");
            exit(0);
        }
        if(fscanf(fsl,"%s%s%f%f%d%d%d%d%s",s1->data.node,s1->data.name,&s1->data.cprice,&s1->data.sprice,&s1->data.y,&s1->data.m,&s1->data.d,&s1->data.cm,s1->data.sname)!=EOF)
        {
            s2->next=s1;
            s2=s2->next;
        }
        else
            break;
    }
}
void show_supply()
{
    supply *s1=slhead->next;
    while(s1->next!=NULL){
        printf("%s %s %5.2f %5.2f %d %d %d %d %s\n",s1->data.node,s1->data.name,s1->data.cprice,s1->data.sprice,s1->data.y,s1->data.m,s1->data.d,s1->data.cm,s1->data.sname);
        s1=s1->next;
    }
}
void delete_supply(supply *&slhead)
{
    supply *s1,*s2;
    s1=slhead;
    while(s1->next!=NULL){
        s2=s1->next;
        free(s1);
        s1=s2;
    }
    free(slhead);
}
//将进货表中商品加到商品库中
void add_goods(goods *&head,supply *s)
{
    goods *p,*t,*t1;
    supply *s1=s;
    p=head->next;
    while(s1->next!=NULL){
       t=(goods*)malloc(sizeof(goods));
       if(t==NULL){
        printf("no enough memory!\n");
        exit(0);
       }
       strcpy(t->data.node,s1->data.node);
       strcpy(t->data.name,s1->data.name);
       t->data.sprice=s1->data.sprice;
       t->data.cprice=s1->data.cprice;
       t->data.y=s1->data.y;
       t->data.m=s1->data.m;
       t->data.d=s1->data.d;
       t->data.cm=s1->data.cm;
       t1=t;
       t1->next=p->next;
       p->next=t1;
       s1=s1->next;
    }
}
int main()
{
    sale_list sl;
    Init_goods();
    Init_VIP();
    Init_staff();
    //check_goods();
    Init_supplier();
    Init_supply();
    Init_sale(sl);
    output_goods(ghead);
    //show_supplier();
    //show_staff(shead);
    //show_goods();
    //show_supply();
    add_goods(ghead,slhead);
    //show_goods();
    //输入一个试试看
    sale_list S;
    Init_sale(S,100);
    deal d1;
    Init_deal(d1);
    char s[50];
    printf("输入收银员号:\n");
    scanf("%s",d1.s_node);
    printf("输入机号：\n");
    scanf("%s",d1.m_node);
    printf("输入商品编号: \n");
    int i=1;
    float money=0;
    int judge=0;
    while(1)
    {
        scanf("%s",s);
        if(strlen(s)==1)
            break;
        goods *g=find_goods(ghead,s);
        strcpy(d1.elem[i].node,g->data.node);
        strcpy(d1.elem[i].name,g->data.name);
        d1.elem[i].num=1;
        d1.elem[i].price=g->data.sprice;
        d1.elem[i].in_price=g->data.cprice;
        find_deal_goods(d1,s);
        i++;
        d1.l++;
    }
    for(int i=1;i<=d1.l;i++)
        money+=d1.elem[i].price*d1.elem[i].num;
    d1.pprice=money;
    d1.s_price=money;
    printf("是否是会员:\n");
    scanf("%d",&d1.if_vip);
    if(d1.if_vip){
        printf("输入会员号:\n");
        scanf("%s",s);
        VIP *v=find_vip(vhead,s);
        if(if_overdue(v)){
            printf("会员已过期！\n");
            d1.if_vip=0;
    }
        else{
            judge++;
            d1.s_price=0.99*money;
        }
    }
    else{
        printf("是否创建新会员卡：\n");
        int If=0;
        scanf("%d",&If);
        if(If){

            char tcode[20],tname[20],ttel[20];
            int tl;
            printf("输入编码:\n");
            scanf("%s",tcode);
            printf("输入姓名:\n");
            scanf("%s",tname);
            printf("输入电话:\n");
            scanf("%s",ttel);
            printf("输入可用时间:\n");
            scanf("%d",&tl);
            add_vip(tcode,tname,ttel,tl,money);
            d1.s_price=money*0.95;
        }
    }

        d1.s_price=money;
    printf("是否活动期间:\n");
    scanf("%d",&d1.if_act);
    if(d1.if_act){
        printf("是否折扣商品:\n");
        scanf("%d",&d1.if_discount);
        if(d1.if_discount);
        d1.s_price=d1.s_price*0.99;
    }
    printf("实收:\n");
    scanf("%f",&d1.g_money);
    d1.p_money=d1.g_money-d1.s_price;
    printf("付款方式:\n");
    scanf("%d",&d1.pay_way);
    date d=get_date();
    printf("===========================\n");
    printf("阳光超市\n");
    printf("%d.%.2d.%.2d %.2d:%.2d\n",d.y,d.m,d.d,d.h,d.mi);
    printf("---------------------------\n");
    printf("品名            数量      小计\n");
    for( i=1;i<=d1.l;i++){
        printf("%s         %d      %5.2f\n",d1.elem[i].node,d1.elem[i].num,d1.elem[i].num*d1.elem[i].price);
        printf("%s\n",d1.elem[i].name);
    }
    printf("---------------------------\n");
    printf("件数：%d",d1.l);
    printf("原价金额:%5.2f\n",d1.pprice);
    printf("应付:%5.2f\n",d1.s_price);
    printf("实收:");
    switch(d1.pay_way){
    case 0:
        printf("现金   ");break;
    case 1:
        printf("支付宝   ");break;
    case 2:
        printf("微信   ");break;
    case 3:
        printf("支付宝   ");break;
    case 4:
        printf("代金券   ");break;
    }
    printf("%5.2f\n",d1.g_money);
    //printf("总额:  %5.2f\n",d1.pprice);
    printf("找零:%5.2f\n",d1.g_money-d1.s_price);
    printf("优惠:%5.2f\n",d1.pprice-d1.s_price);
    date td=get_date();
    printf("小票号:");
    printf("%d%d%d",td.y,td.m,td.d);
    printf("000001\n");
    printf("谢谢惠顾，欢迎下次光临\n");
    printf("===========================\n");
    build_sale(sl,d1);
    for(int i=1;i<=sl.length;i++)
        printf("%s %7.2f %7.2f %d %7.2f %7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);
    //show_goods();
    if((p1=fopen("xiaopiaos.txt","r+"))==NULL){
        printf("can not open xiaopiaoxs.txt\n");
        exit(0);
    }
    if((p2=fopen("after_xiaopiaos.txt","w+"))==NULL){
        printf("can not build nex txt\n");
        exit(0);
    }
    int c=0;
    while(1){
            c++;
        char s1[20],s2[20];
        if(fscanf(p1,"%s%s",s1,s2)!=EOF){
            deal d2;
            Init_deal(d2);
            fscanf(p1,"%d%d%d",&d2.if_vip,&d2.if_act,&d2.l);
            char vs[50];
            for(int i=1;i<=d2.l;i++){
              if(d2.if_vip){
                fscanf(p1,"%s",vs);
              }
              fscanf(p1,"%s",s);
              goods *g2=find_goods(ghead,s);
              strcpy(d2.elem[i].node,g2->data.node);
              strcpy(d2.elem[i].name,g2->data.name);
              d2.elem[i].in_price=g2->data.cprice;
              d2.elem[i].num=1;
              d2.elem[i].price=g2->data.sprice;
              //find_deal_goods(d2,s);
            }
            money=0;
            for(int i=1;i<=d2.l;i++)
                money+=d2.elem[i].price*d2.elem[i].num;
            d2.pprice=d2.s_price=money;
            if(d2.if_vip){
               VIP *v2=find_vip(vhead,vs);
               if(if_overdue(v2))
                d2.if_vip=0;
            }
            if(d2.if_vip)
                d2.s_price=0.99*d2.s_price;
            if(d2.if_act)
                d2.s_price=d2.s_price*0.99;
            fscanf(p1,"%f",&d2.g_money);
            fscanf(p1,"%d",&d2.pay_way);
            fprintf(p2,"%s\n","-------------------------------");
            fprintf(p2,"%s","     阳光超市\n");
            fprintf(p2,"%s %s  %s %s\n","收银员：",s1,"机号",s2);
            d=get_date();
            fprintf(p2,"%d.%.2d.%.2d %.2d:%.2d\n",d.y,d.m,d.d,d.h,d.mi);
            fprintf(p2,"%s\n","===============================");
            fprintf(p2,"%s \n","条码/品名        数量        小计\n");
            for(int i=1;i<=d2.l;i++){
                fprintf(p2,"%s    %d    %7.2f\n",d2.elem[i].node,d2.elem[i].num,d2.elem[i].price);
                fprintf(p2,"%s\n",d2.elem[i].name);
            }
            fprintf(p2,"%s\n","==============================");
            fprintf(p2,"%s %d %s %7.2f\n","件数：",d2.l,"原价金额",money);
            fprintf(p2,"%s","实收：");
            switch(d2.pay_way){
        case 0:
            fprintf(p2,"%s","现金    ");break;
        case 1:
            fprintf(p2,"%s","支付宝  ");break;
        case 2:
            fprintf(p2,"%s","微信   ");break;
        case 3:
            fprintf(p2,"%s","银行卡  ");break;
        case 4:
            fprintf(p2,"%s","代金券  ");break;
            }
            fprintf(p2,"%5.2f\n",d2.g_money);
            fprintf(p2,"应付：%5.2f\n",d2.s_price);
            fprintf(p2,"找零：%5.2f\n",d2.g_money-d2.s_price);
            fprintf(p2,"优惠：%5.2f\n",d2.pprice-d2.s_price);
            date ttd=get_date();
            fprintf(p2,"%s","小票号：");
            fprintf(p2,"%2d%2d%2d",ttd.y,ttd.m,ttd.d);
            fprintf(p2,"%.5d\n",c);
            fprintf(p2,"%s","--------------------------------\n");
            fprintf(p2,"%s","\n");
            fprintf(p2,"%s","\n");
            build_sale(sl,d2);
        //for(int i=1;i<=sl.length;i++)
             //printf("%s %7.2f %7.2f %d %7.2f %7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);
               delete_deal(d2);
            }
            else
                break;
    }
     FILE *fr;
    if((fr=fopen("rank.txt","w+"))==NULL)
    {
        printf("can not build rank.txt!\n");
        exit(0);
    }
   // show_sale(sl);
    fprintf(fr,"%s","按照销售额排名：\n");
    rank1(sl);
     for(int i=1;i<=sl.length;i++)
             fprintf(fr,"%-20s %-7.2f %-7.2f %-5d %-7.2f %-7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);
   // show_sale(sl);
    fprintf(fr,"%s","按照销售量排名：\n");
    rank2(sl);
    //show_sale(sl);
     for(int i=1;i<=sl.length;i++)
             fprintf(fr,"%-20s %-7.2f %-7.2f %-5d %-7.2f %-7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);
    //printf("按照销售利润排名:\n");
    fprintf(fr,"%s","按照销售利润排名:\n");
    rank3(sl);
     for(int i=1;i<=sl.length;i++)
             fprintf(fr,"%-20s %-7.2f %-7.2f %-5d %-7.2f %-7.2f\n",sl.elem[i].name,sl.elem[i].in_price,sl.elem[i].price,sl.elem[i].sale_num,sl.elem[i].sale_money,sl.elem[i].profit);
    //show_sale(sl);
    rank1(sl);
    Delete_sale(sl);
    output_goods(ghead);
    output_vip(vhead);
    delete_goods(ghead);
    delete_VIP(vhead);
    delete_deal(d1);
    delete_staff(shead);
    delete_supplier(suhead);
    delete_supply(slhead);
    fclose(p1);
    fclose(p2);
    fclose(fp);
    fclose(fg);
    fclose(fs);
    fclose(fsu);
    fclose(fsl);
    return 0;
}


