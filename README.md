<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <Grid.Resources>
            <DataTemplate x:Key="Wzor">
                <TextBox x:Name="KomText" Text="{Binding Value, Mode=TwoWay}" HorizontalAlignment="Stretch" VerticalAlignment="Center" Margin="1" Width="50" Height="50"/>
            </DataTemplate>
        </Grid.Resources>

        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <ItemsControl Grid.Row="0" Grid.Column="0" ItemsSource="{Binding Siatka}" Margin="1">
            <ItemsControl.ItemsPanel>
                <ItemsPanelTemplate>
                    <UniformGrid Rows="4" Columns="4" />
                </ItemsPanelTemplate>
            </ItemsControl.ItemsPanel>
            <ItemsControl.ItemTemplate>
                <DataTemplate>
                    <ContentControl Content="{Binding}"
                                    ContentTemplate="{StaticResource Wzor}" />
                </DataTemplate>
            </ItemsControl.ItemTemplate>
        </ItemsControl>
        <Button Grid.Row="1" Grid.Column="1" Content="Sprawdź" Click="CheckButton_Click" Margin="5"/>
    </Grid>
</Window>






using System;
using System.Windows;

namespace WpfApp1
{
    public partial class MainWindow : Window
    {
        private SudokuGra sudokuGra;

        public MainWindow()
        {
            InitializeComponent();
            sudokuGra = new SudokuGra();
            DataContext = sudokuGra;
        }

        private void CheckButton_Click(object sender, RoutedEventArgs e)
        {
            if (sudokuGra.IsSolved())
            {
                MessageBox.Show("Gratulacje! Sudoku zostało rozwiązane poprawnie");
            }
            else
            {
                MessageBox.Show("Sudoku jest jeszcze niekompletne lub zawiera błędy");
            }
        }
    }




    using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace WpfApp1
{
    public class SudokuKomora
    {
        public int Value { get; set; }

    }
}






using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using WpfApp1;

public class SudokuGra
{
    public ObservableCollection<SudokuKomora> Siatka { get; private set; }

    public SudokuGra()
    {
        Siatka = new ObservableCollection<SudokuKomora>();
        InitializeGrid();
    }

    public void InitializeGrid()
    {
        for(int i = 0; i < 16; i++)
        {
            Siatka.Add(new SudokuKomora { Value = 0 });
        }
    }

    public bool IsSolved()
    {
        //Soprawdzanie czy wartości o 1 do 4
        foreach(var cell in Siatka)
        {
            if(cell.Value < 1 || cell.Value > 4)
            {
                return false;
            }
        }
        //Sprawcdznie poprawnmości
        for(int i = 0; i < 4; i++)
        {
            for(int j = 1; j <= 4; j++)
            {
                if(!RzadCheck(i, j) || !KolumnaCheck(i, j) || !KwadratCheck(i, j))
                {
                    return false;
                }
            }
        }
        return true;
    }


    private bool RzadCheck(int rzadI, int value)
    {
        int licz = 0;
        for(int i=0; i<4;i++)
        {
            if(Siatka[rzadI * 4+i].Value == value)
            {
                licz++;
            }
        }
        return licz <= 1;
    }

    private bool KolumnaCheck(int colI, int value)
    {
        int licz = 0;
        for(int i = 0; i < 4; i++)
        {
            if(Siatka[colI+ i*4].Value == value)
            {
                licz++;
            }
        }
        return licz <= 1;
    }

    private bool KwadratCheck(int kwaI, int value)
    {
        int startRzad = (kwaI/2)*2;
        int startKol = (kwaI%2) *2;
        int licz = 0;
        for(int i =startRzad; i <startRzad + 2; i++)
        {
            for(int j = startKol;j <startKol +2;j++)
            {
                if(Siatka[i*4+j].Value == value)
                {
                    licz++;
                }
            }
        }
        return licz <= 1;
    }
}

    

}
