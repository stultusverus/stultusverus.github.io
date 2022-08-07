---
layout: post
title:  "Add Old Text as an Argument of QComboBox::activated"
date:   2022-08-07 18:00:00 +0900
categories: Qt
---
So I'm making these grouped combo boxes. I want them to all have unique selections, so when the selection of one is changed, I need to add the old selection to other combo boxes and remove the new. Obviously I need to use the *QComboBox::activated* signal, but unfortunately it does not tell what the text was.

Apparently we have to derive from QComboBox a new class to solve this (I overrided some functions to not track programatic changes):

{% highlight c++ %}
class TrackedComboBox : public QComboBox {
  Q_OBJECT
 private:
  QString _old_text;
 private slots:
  void textActivatedSlot(const QString &);

 public:
  explicit TrackedComboBox(QWidget *parent = nullptr);
  void setCurrentIndex(int index);
  void setCurrentText(const QString &text);
 signals:
  void textActivated2(const QString &oldText, const QString &newText);
};

TrackedComboBox::TrackedComboBox(QWidget *parent) : QComboBox(parent) {
  _old_text = this->currentText();
  connect(this, SIGNAL(textActivated(const QString &)), this,
          SLOT(textActivatedSlot(const QString &)));
}
void TrackedComboBox::textActivatedSlot(const QString &newText) {
  qDebug() << __func__ << _old_text << "->" << newText;
  emit textActivated2(_old_text, newText);
  _old_text = newText;
}
void TrackedComboBox::setCurrentIndex(int index) {
  QComboBox::setCurrentIndex(index);
  _old_text = this->currentText();
}
void TrackedComboBox::setCurrentText(const QString &text) {
  QComboBox::setCurrentText(text);
  _old_text = text;
}
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
