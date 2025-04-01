# sentiment-Analysis-
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'app-sentiment-dashboard',
  templateUrl: './sentiment-dashboard.component.html',
  styleUrls: ['./sentiment-dashboard.component.css']
})
export class SentimentDashboardComponent implements OnInit {
  sentimentData: any[] = [];

  constructor(private http: HttpClient) { }

  ngOnInit(): void {
    this.getSentimentData();
  }

  getSentimentData() {
    this.http.get('https://api.example.com/sentiment')
      .subscribe(data => {
        this.sentimentData = data as any[];
      });
  }
}
##Backend
const AWS = require('aws-sdk');
const kinesis = new AWS.Kinesis();

exports.handler = async (event) => {
    const records = event.Records; // Records coming from Kinesis stream
    let sentimentResults = [];

    // Loop over each record and process sentiment
    for (let record of records) {
        const payload = JSON.parse(Buffer.from(record.kinesis.data, 'base64').toString('utf8'));
        const sentiment = analyzeSentiment(payload.text); // Placeholder for sentiment analysis function
        sentimentResults.push({
            id: payload.id,
            sentiment: sentiment
        });
    }

    // Push processed data to a new Kinesis stream for visualization
    const params = {
        StreamName: 'ProcessedSentimentStream',
        Records: sentimentResults.map(result => ({
            Data: JSON.stringify(result),
            PartitionKey: result.id
        }))
    };

    try {
        await kinesis.putRecords(params).promise();
        return {
            statusCode: 200,
            body: JSON.stringify({ message: 'Sentiment data processed successfully' })
        };
    } catch (err) {
        return {
            statusCode: 500,
            body: JSON.stringify({ message: 'Error processing sentiment', error: err })
        };
    }
};

function analyzeSentiment(text) {
    // Placeholder for sentiment analysis logic
    return Math.random() > 0.5 ? 'positive' : 'negative';
}
