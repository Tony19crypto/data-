import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

def load_and_clean_data():
    # Load data
    df = pd.read_csv('fcc-forum-pageviews.csv', index_col='date', parse_dates=True)

    # Clean data: Remove the top and bottom 2.5% of page views
    lower_bound = df['value'].quantile(0.025)
    upper_bound = df['value'].quantile(0.975)
    df = df[(df['value'] >= lower_bound) & (df['value'] <= upper_bound)]

    return df

def draw_line_plot():
    df = load_and_clean_data()

    # Plot
    plt.figure(figsize=(12, 6))
    plt.plot(df.index, df['value'], color='red', linewidth=1)
    plt.title('Daily freeCodeCamp Forum Page Views 5/2016-12/2019')
    plt.xlabel('Date')
    plt.ylabel('Page Views')

    # Save plot
    plt.savefig('line_plot.png')
    return plt.gcf()

def draw_bar_plot():
    df = load_and_clean_data()

    # Prepare data for bar plot
    df['year'] = df.index.year
    df['month'] = df.index.month
    df_bar = df.groupby(['year', 'month'])['value'].mean().unstack()

    # Plot
    ax = df_bar.plot(kind='bar', figsize=(12, 6), legend=True)
    ax.set_title('Average Daily Page Views per Month')
    ax.set_xlabel('Years')
    ax.set_ylabel('Average Page Views')
    ax.legend(title='Months', labels=[
        'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
    ])

    # Save plot
    plt.savefig('bar_plot.png')
    return ax.get_figure()

def draw_box_plot():
    df = load_and_clean_data()

    # Prepare data for box plots
    df['year'] = df.index.year
    df['month'] = df.index.strftime('%b')
    df['month_num'] = df.index.month
    df = df.sort_values('month_num')

    # Plot
    fig, axes = plt.subplots(1, 2, figsize=(16, 6))

    sns.boxplot(x='year', y='value', data=df, ax=axes[0])
    axes[0].set_title('Year-wise Box Plot (Trend)')
    axes[0].set_xlabel('Year')
    axes[0].set_ylabel('Page Views')

    sns.boxplot(x='month', y='value', data=df, ax=axes[1])
    axes[1].set_title('Month-wise Box Plot (Seasonality)')
    axes[1].set_xlabel('Month')
    axes[1].set_ylabel('Page Views')

    # Save plot
    plt.savefig('box_plot.png')
    return fig

# Main script for testing
if __name__ == "__main__":
    draw_line_plot()
    draw_bar_plot()
    draw_box_plot()
