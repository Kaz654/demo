using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

try
{
    var user = (from x in db.Users
                where x.login == TextBox1.Text
                select x).ToArray();
    if (TextBox1.Text.Length == 0 || TextBox2.Text.Length == 0)
    {
        MessageBox.Show("Поля Логин и Пароль обязательны для заполнения",
        "!", MessageBoxButton.OK, MessageBoxImage.Error);
    }
    else
    {
        if (TextBox1.Text == user[0].login)
        {
            if (TextBox2.Text == user[0].password)
            {
                if (user[0].date != null)
                {
                    // вычисляем количество дней, прошедших после последнего входа пользователя
                     DateTime d1 = DateTime.Now;
                    DateTime d2 = Convert.ToDateTime(user[0].date);
                    TimeSpan d = d1 - d2;
                    // если прошло более 30 дней, блокируем запись
                    if (Convert.ToInt32(d.ToString("dd")) > 30)
                    {
                        user[0].active = false;
                        db.SaveChanges();
                    }
                }
                if (user[0].active == true)
                {
                    switch (user[0].role2)
                    {
                        case 1:
                            MessageBox.Show("Добро пожаловать. Вы успешно авторизировались как администратор " + user[0].surname, "Уведомление", MessageBoxButton.OK,
                           MessageBoxImage.Information);
                            Window1 window = new Window1();
                            window.Show();
                            break;
                        case 4:
                            if (user[0].date == null)
                            {
                                usID = user[0].ID;
                                MessageBox.Show("Добро пожаловать. При первом входе в систему необходимо изменить пароль" ,
                                "Уведомление", MessageBoxButton.OK,
                                MessageBoxImage.Information);
                                UpdatePass window3 = new UpdatePass(usID);
                                window3.Show();
                            }
                            else
                            {
                                MessageBox.Show("Добро пожаловать. Вы успешно авторизировались как пользователь " + user[0].surname, "Уведомление", MessageBoxButton.OK,
                               MessageBoxImage.Information);
                                Window2 window1 = new Window2();
                                window1.Show();
                            }
                            break;
                        default:
                            MessageBox.Show("Данные не обнаружены", "Уведомление",
                           MessageBoxButton.OK, MessageBoxImage.Information);
                            break;
                    }
                    user[0].date = DateTime.Now;
                    user[0].count = 0;
                    db.SaveChanges();
                }
                else
                {
                    MessageBox.Show("Вы заблокированы. Обратитесь к администратору системы.", "Уведомление", MessageBoxButton.OK, MessageBoxImage.Information);
                }
            }
            else
            {
                if (user[0].count > 2)
                {
                    user[0].active = false;
                    db.SaveChanges();
                    MessageBox.Show("Вы заблокированы. Обратитесь к администратору системы.",
                    "Уведомление", MessageBoxButton.OK,
                    MessageBoxImage.Information);
                }
                else
                {
                    MessageBox.Show("Вы ввели неверный логин или пароль",
                   "Пожалуйста проверьте ещё раз введенные данные",
                    MessageBoxButton.OK, MessageBoxImage.Error);
                    user[0].count++;
                    db.SaveChanges();
                }
            }
        }
    }
}
catch (SystemException)
{
    MessageBox.Show("Ошибка системы", "Ошибка", MessageBoxButton.OK,
   MessageBoxImage.Error);
}
