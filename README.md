package main

import (
	"log"
	"os"

	"github.com/spf13/cobra"
	"gopkg.in/telegram-bot-api.v4"
)

var rootCmd = &cobra.Command{
	Use:   "telegram-bot",
	Short: "A simple Telegram bot",
	Run: func(cmd *cobra.Command, args []string) {
		token := os.Getenv("TELE_TOKEN")
		bot, err := tgbotapi.NewBotAPI(token)
		if err != nil {
			log.Panic(err)
		}

		bot.Debug = true

		log.Printf("Authorized on account %s", bot.Self.UserName)

		u := tgbotapi.NewUpdate(0)
		u.Timeout = 60

		updates, err := bot.GetUpdatesChan(u)

		for update := range updates {
			if update.Message == nil { // ignore any non-Message Updates
				continue
			}

			log.Printf("[%s] %s", update.Message.From.UserName, update.Message.Text)

			msg := tgbotapi.NewMessage(update.Message.Chat.ID, update.Message.Text)
			bot.Send(msg)
		}
	},
}

func init() {
	rootCmd.Execute()
}

func main() {}
