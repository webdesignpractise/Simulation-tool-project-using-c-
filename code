#include<simplecpp>
#include<iostream>
/*
    A redumentry resistive circuit editor with a GUI. Operations:

    -->Click on the button "Node", then click on canvas. This creates a node
       to which other components can be attatched.

    -->Click on button "Conductance",("source"), then click on twonodes. Then
       type a value fromthe keyboard into the shell window. This creates a
       Conductance (current source) of that value between the clicked nodes.

    -->click on button "Solve". This solves the circuit and prints voltage
       values inthe shell window.

    -->Click  on button "Quit". This quits.


*/
struct Button{
   int x,y,w,h;
   Button(const char* name,int xx,int yy,int ww,int hh){
    x=xx;
    y=yy;
    w=ww;
    h=hh;
    Rectangle(x,y,w,h).imprint();
    Text(x,y,name).imprint();
   }
   bool in(int click){
    int cx=click/65536,cy=click%65536;
    return (abs(cx-x)<= w && abs(cy-y)<=h);
   }
};
struct Node{
    const double r=20;
    double x,y;
    Node(int click,int index){

        x=click/65536;
        y=click%65536;
        Circle(x,y,r).imprint();
        Text(x,y,index).imprint();
    }
    bool in(int click)
    {
        return pow((x-click/65536),2)+pow((y-click%65536),2)<pow(r,2);
    }
};
struct MathRep{
    vector<vector<double>>A;//conductance matrix
    vector<double> b;
    //b[i] : total current entering ithrough current source
     void addNode(){
        A.push_back(vector<double>(A.size(),0));//all 0 rows
        for(size_t i=0;i<A.size();i++) //add 0 columns
            A[i].push_back(0);
        b.push_back(0);
     }
     void addSource(int i,int j,double val){
        b[i]-=val;
        b[j]+=val;
     }
     void addConductance(int i,int j,double val){
        A[i][i]+=val;
        A[j][j]+=val;
        A[i][j]-=val;
        A[j][i]-=val;
     }
     void solve(){
        // Solving for x[i]=voltage of node i.
        //(Ax)[i] : current leaving node i through conductance
        //must equal b[i]=current entering node i through current source
        //so solve Ax=b
        //Use row i to eliminate 0s in column j!=i
        //This will yeild x[i]=b[i]/A[i][i]
        for(size_t i=0;i<A.size()-1;i++){
            for(size_t j=0;j<A.size();j++){
                if(i!=j){
                    double mplier=A[j][i]/A[i][i];
                    for(size_t k=0;k<A.size()-1;k++)
                        A[j][k]-=A[i][k]+mplier;
                    b[j]=b[j]-b[j]*mplier;
                }
            }

        }
        cout<<"Printing node potentials: "<<endl;
        for(size_t i=0;i<A.size()-1;i++)
            cout<<i<<": "<<b[i]/A[i][i]<<endl;
        cout<<A.size()-1<<": "<<0<<endl;
     }
};
struct CanvasContent{
    vector<Node*> Nptr;
    MathRep *mrp;
    CanvasContent(MathRep *mrp0)
    {
        mrp=mrp0;
    }
    void addNode(){
       int vclick=getClick();
       Nptr.push_back(new Node(vclick,Nptr.size()));//index of node
       mrp->addNode();
    }
    int selectNode(){
        int click=getClick();
        for(size_t i=0;i<Nptr.size();i++)
            if(Nptr[i]->in(click))return i;
        return -1;
    }
    void addConductance(){
        int i1=selectNode(),i2=selectNode();
        if(i1>=0 && i2>=0){
            double x1=Nptr[i1]->x,y1=Nptr[i1]->y,x2=Nptr[i2]->x,y2=Nptr[i2]->y;
            Line(x1,y1,x2,y2).imprint();
            cout<<"Conductance value: ";
            double g;cin>>g;
            Text((x1+x2)/2,(y1+y2)/2,g).imprint();
            mrp->addConductance(i1,i2,g);
        }
        else
            cout<<"Select operation again."<<endl;
    }
    void addSource(){
        int i1=selectNode(),i2=selectNode();
        if(i1>=0 && i2>=0){
            double x1=Nptr[i1]->x,y1=Nptr[i1]->y,x2=Nptr[i2]->x,y2=Nptr[i2]->y;
            Line(x1,y1,x2,y2).imprint();
            cout<<"Current source strength: ";
            double j;cin>>j;
            mrp->addSource(i1,i2,j);
            Text((x1+x2)/2,(y1+y2)/2,j).imprint();
            Turtle t;t.penUp();
            t.moveTo((x1+x2)/2,(y1+y2)/2);
            t.face(x2,y2);
            t.forward(10);
            t.imprint();
        }
        else
            cout<<"Select operation again."<<endl;
    }

};
int main(){
//Write your code here
    initCanvas("Circuit Editor",600,600);
    Button b1("Node",100,50,160,30);
    Button b2("Conductance",100,150,160,30);
    Button b3("Source",100,250,160,30);
    Button b4("Solve",100,350,160,30);
    Button b5("Quit",100,450,160,30);

    MathRep mr;             //Mathematical representation of circuit
    CanvasContent cc(&mr);  //everything on canva exceptbuttons

    while(true)
    {
        int cpoint=getClick();
        if(b1.in(cpoint)) cc.addNode();
        else if(b2.in(cpoint)) cc.addConductance();
        else if(b3.in(cpoint)) cc.addSource();
        else if(b4.in(cpoint)) mr.solve();
        else if(b5.in(cpoint)) break;
    }

}
